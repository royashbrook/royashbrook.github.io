---
layout: post
published: true
title: Extract data from Excel Spreadsheet using PowerShell
subtitle: Why? Well... because we can!!!
date: '2020-09-02'
---
I have a dashboard app that visualizes data pulled from a number of difference places. One source that I have avoided (up until now) is excel. However, I finally did receive a request to extract some information from 'a finance spreadsheet' and put it out there. So off I went.

This is one of those complicated financial spreadsheets that is someone's baby and probably should be in an actual app, but is not. I'm pretty sure every company has a person somewhere with 'the spreadsheet' and those spreadsheets always seem to have hundreds of tabs and tables and all kinds of fun things in them. So, it's pretty big to just copy around at over 30MB, but fortunately it's pretty permanent. I already have a fairly simple template I use for data movement with powershell that basically equates to 'config, get data, use data, cleanup' that runs for dozens of jobs. This follows that pattern, so just need to nail down how to get the data out and package it as the 'use data' portion that will move the job is already solved. The search begins:

## Option 1 - OpenXML SDK

Found this:
https://www.codeproject.com/Questions/1247310/How-do-I-read-the-value-of-a-formula-in-a-cell-ins

Led to this:
https://docs.microsoft.com/en-us/office/open-xml/how-to-retrieve-the-values-of-cells-in-a-spreadsheet?redirectedfrom=MSDN

And downloaded this:
https://www.microsoft.com/en-us/download/details.aspx?id=30425

I just got the SDK itself and extracted the files. You can look at msiexec (no args) to see help, or here was a good article I found on this topic. https://superuser.com/questions/307678/how-do-i-extract-files-from-an-msi-package. I may have described this process in the past for the sharepoint 2013 sdk, I forget. Regardless, here's a little powershell if you need it. =)

```PowerShell

$f = gci OpenXMLSDKV25.msi
$in = $f.FullName
$out = $f.Directory.FullName + "\out"
msiexec /a "$in" TARGETDIR="$out" /qn

```

Note that the fullname of the archive doesn't seem to be needed if it's in the current dir, but the targetdir seems to need a full path. So I just did a gci and used fullname on both of them and appended out. It seems to create the directory with no issues if it's not there.

Ok, so now I copy the dll from lib up to the current folder

`copy .\out\lib\*.dll`

So now I have my OpenXml file I need. It's called `DocumentFormat.OpenXml.dll`. Here is some code to pull some cells out of that spreadsheet.

```PowerShell

$fileName = (gci '.\ImportantSpreadsheet.xlsx').FullName
$wksName = "Name of Sheet with our Cells"
$cells = [char[]]([int][char]'C'..[int][char]'X') | %{$_+"2"}
[Reflection.Assembly]::LoadFile((Get-Item .\DocumentFormat.OpenXml.dll ).FullName)
$document = [DocumentFormat.OpenXml.Packaging.SpreadsheetDocument]::Open($fileName, $false)
$workBookPart = $document.WorkbookPart
$workBook = $workBookPart.Workbook
$sheets = $workBook.Sheets
$sheetID = ($sheets | where name -like $wksName)[0].id.value
$workSheetPart = $workBookPart.GetPartById($sheetID)
$cellData = $workSheetPart.Worksheet.Descendants() |
    ?{$cells -contains $_.CellReference} |
    %{ $_.CellValue.Text}
$cellData

```

It took me a little bit to work this out, but this does the job to just get the values. There are lots of examples for c#, but i didn't see many for powershell that worked for what i wanted. The biggest challenge I ran into was the fact that a lot of the properties just return nothing if you have them in their incorrectly. As a perfect example, if you omit the last line that gets the Text property from CellValue, you will get no data. Same if you just remove Text, you'll get results, just empty. In my experience this is kind of par for the course working with a lot of external assemblies like this. Regardless, the above worked fine to get me the data. But...

## Option 2 - Direct Extraction

I wasn't excited about having to include a dependency with the job and I had hoped that the SDK would be a little more intuitive than some other dlls I have included with other jobs. So since OpenXML is really just XML anyway, and an xlsx is really just some zipped up data, I thought I would look at just directly extracting the information. I feel like I may have done this at some point in the past, but apparently that memory is not popping up, and why not take a fresh look.

After probably way too much googling for options, I ultimately settled on this way of getting the single xml file out of the excel file:

```PowerShell

# shell method

#extract sheet we want
$shell= New-Object -Com Shell.Application
$infile = (Resolve-Path spreadsheet.zip).Path
$outfolder = (Resolve-Path .\).Path
$zip = $shell.NameSpace($infile).Items()
$xl = ($zip | where name -eq xl).GetFolder.items()
$worksheets = ($xl | where name -eq worksheets).getFolder.items()
$files = $worksheets | where name -eq sheet42.xml
$opt = 0x14 #silent, overwrite
$shell.NameSpace($outfolder).copyhere($files,$opt)

#get data out of sheet
[xml]$sheet = gc .\t\xl\worksheets\sheet77.xml
$cells = [char[]]([int][char]'C'..[int][char]'X') | %{$_+"2"}
$values = $sheet.worksheet.sheetData.row.c | ?{$cells -contains $_.r} | select v

```

At least I settled on that until I stumbled across something that jogged my memory about other ways to unzip things, and I ultimately went with this:

```PowerShell

# zipfile method

function getOneFileFromZip($zipPath,$fileName){
    $zip = [io.compression.zipfile]::OpenRead($zipPath)
    $file = $zip.entries | Where-Object{$_.Name -eq $fileName}
    $stream = $file.Open()
    $reader = New-Object IO.StreamReader($file.Open())
    $reader.ReadToEnd()
    [void]$reader.Dispose()
    [void]$stream.Dispose()
    [void]$zip.Dispose()
}

#get data out of sheet
# $s here is just a hashtable with some settings
[xml]$sheet = getOneFileFromZip (join-path $s.path $s.file) $s.xmlfile
$values = $sheet.worksheet.sheetData.row.c |
    Where-Object{$s.cells -contains $_.r} |
    select v

```

> EDIT: After trying this on a few other computers, I ran into too many instances where I needed the assembly reference. So I went ahead and added this to the top of the function (could be any where though) `Add-Type -AssemblyName System.IO.Compression.FileSystem` and thenreplaced the line `$zip = [io.compression.zipfile]::OpenRead($zipPath)` with `$zip = [System.IO.Compression.ZipFile]::OpenRead($zipPath)` and seemed to have no issues after that.

The top Shell.Application method above is not in a function because I was still testing things. I *think* that the file had to be named with a `.zip` extension for the Shell to work right, but maybe something else was being squirrely. Now io.compression.zipfile seems to work fine on newer windows without any references, but you could possibly need to add that assembly. I believe that would be as simple as `Add-Type -AssemblyName system.io.compression` but YMMV.

Regardless of the two methods above chosen, ultimately you end up with the xml file contents and then you filter them. You can copy out the file and get-contents on that file, or you can do something like with zipfile and return just that one file from within the zip.

## Conclusion and Notes

I went with the Direct Extract method using zipfile. Pretty simple conclusion. =)

Some items of note:

1. I prefer not to use dependencies unless they make things faster or make the code simpler, using the SDK did neither for me.
2. Speed was about the same. Very subjective measurements as I just eyeballed things. But this is an infrequently run job. Days or more between runs so the difference between 1 second and 2 seconds isn't something that would matter and they were all pretty quick.
3. Using zipfile I was able to simply open the file directly from it's location on the network vs copying it down, no renaming was needed, and I did not need to save the file out and then clean it up later. In memory wins for me.
4. The 'Cells' in question I needed was a range from one single row. So in the code above I just have some PowerShell to generate an array of values for C2-X2. That is a made up set of cells and ultimately I just enumerated the specific cells I needed in my settings file and used that list. I found using this sequential/generated value was easier for my testing though as I could easily grow/shrink things or swap rows pretty easily to validate the data was being pulled correctly. They could be all out of order and completely unrelated as long as you have them in an array of strings with cell names like C1,ZZ4,X99 or whatever.
5. I did not discuss the structure of the XML files here, but you can see that after getting `$sheet` we pull out the rows and get a 'c' from `$sheet.worksheet.sheetData.row.c` and ultimately check an 'r' value for a cell match. If you want to see one of these files, you can just rename an xlsx to .zip and open it or right click and extract if you have 7zip installed or something. The OpenXML SDK documentation has tons of information about the schema, but ultimately it's a pretty simple process of just looking for the cells you want and pulling out the data as I've shown above.
6. I also did not talk about how I know the file inside I wanted to get. There may be a way to see this in excel, but in my case I had 2 ways. First, when you are using the OpenXML SDK, you get this information. There may be other ways, but the way I pulled this data with the SDK was to search by the sheet name and then I got the ID to use later with this line `$sheetID = ($sheets | where name -like $wksName)[0].id.value`. So I knew what the sheetID was going into things. This doesn't give you the file name, but you can just open up the xml zipped file and look for that ID or the name to find it. Second, when you open up the xml zipped file and just look for the name, you will find a mapping table of sorts in XML that has the sheets and their correlated IDs. You can also search for values if you know them or other items and then see in the XML if it's the correct sheet. That's what I did anyway. The xml is not 'easy' to read, but I just formatted it in vscode and then it was fairly simple to scroll through and find what i needed. And since I was scrolling through a 3MB XML file, I'm guessing it's not too much worse than some of these pages for most people.

> EDIT: I needed to switch to a different spreadsheet at a later time, and so needed to find the ID again. I figured I would put a note here on how I did that. Firstly, you need to start with the worksheet name and the cells you want to get. The cells are easy, but if you have a worksheet with ~100 worksheets like I did, finding the worksheet is the tough part. Right clicking the file xlsx file should allow you to just unzip it into a folder. In that folder, there is an 'xl' folder and in there is a workbook.xml file. You can open this file in a text editor and search for the worksheet name. This will show you some xml element like this `<sheet name="sheet name you are looking for" sheetId="77" r:id="rId99"/>`. We need the value `rId99` in this case. Now we need to check another file. under the 'xl' folder, there is also another folder named '_rels' and in that folder there is a workbook.xml.rels file. If you search in that file, you should find some element like this `<Relationship Id="rId99" Type="http://schemas.openxmlformats.org/officeDocument/2006/relationships/worksheet" Target="worksheets/sheet39.xml"/>` and now we know which sheet to extract. There is a lot of documentation on the openxml format on the MS site, but none of it was that clear. I stumbled across this the first time by just doing text searches in all the files, but that wasn't doing it for me this time. That is what brought me *back* to this article this time, even though the answer wasn't here originally. After some more trial and error and some more searching, i found (this answer)[https://stackoverflow.com/questions/45888793/openxml-c-sharp-what-determines-the-name-of-sheet-xml-in-open-office-file-st] on stackoverflow which had the nitty gritty of what I needed even thought it was a different question. Hope this helps someone, or at least future me again. =)

I hope this helps someone, or at least helps out future me! =)
