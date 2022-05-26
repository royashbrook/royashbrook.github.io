---
layout: post
title: How to search in a SharePoint 2007 list using JavaScript
---

So recently I had this great idea to extract a bunch of documentation from a sort-of proprietary system, turn it into diagrams, and put it on SharePoint with some searchable data bout each diagram. So the solution looks like:

- Save all the Diagrams as files with some kind of unique name to some location.
- Extract a bunch of data bout the Diagrams (preferably the actual text in them) into excel with the same unique name as a key
- Upload the whole document into a new SharePoint list so it would be searchable and people that don’t have access to the source system could see the documentation.

**Note: Make sure you add a column that points to the extracted diagrams.**

Sounds pretty simple, but I ran into the little hitch that SharePoint apparently doesn’t seem to index list data by default. Bummer. So after looking for a way to search that data, the quickest way to get this to work (keeping in mind I don’t have control over the sharepoint server, just my site) was to utilize the Content Editor Web Part (CEWP) and JavaScript. This consisted of the following steps:

- Make sure your list shows *EVERYTHING*, this will only filter what is actually on the current page
- Add a Content Editor Web Part at the top of the list page
- Paste in the code down below

That’s it. The JavaScript listed will search whatever is on the page in that table and hide it if it doesn’t match the text within that row. The button text box will call the JavaScript when you hit enter after typing. Not perfect, but it works so far and everyone is happy with the results.

```html
<script type="text/javascript"> 
function getElementsByCssClass(sTagName, sClassName) { 
    var results = new Array(); 
    var allTagElements = document.getElementsByTagName(sTagName); 
    for (i = 0; i < allTagElements.length; i++) { 
        if(allTagElements[i].className == sClassName) results.push(allTagElements[i]); 
    } 
    return results; 
}

function getTxt(obj) { 
    if(obj.innerText) return obj.innerText; 
    else return obj.textContent; 
}

var lvRows = null;

function quickSearch(term) { 
    if(lvRows == null) { 
        var lvTable = getElementsByCssClass("table","ms-listviewtable")[0]; 
        lvRows = lvTable.childNodes[0].childNodes; 
    } 
    for(i = 1; i < lvRows.length; i++) { 
        if(getTxt(lvRows[i]).toLowerCase().indexOf(term.toLowerCase()) > -1) { 
            lvRows[i].style.display = "table-row"; 
        } 
        else lvRows[i].style.display = 'none'; 
    } 
}
</script>

Quick Search (type your query then hit 'Enter'): <input class="ms-long" id="inputboxID" type="text" onkeyup="if (event.keyCode == 13) quickSearch(this.value)"/>
```

Looked at a bunch of stuff, but these were the good sources:

- The Search
    - http://www.sharepointboris.net/articles/the-power-of-content-editor-webpart/3-quick-search-in-list-view/
- The Enter Check
    - http://stackoverflow.com/questions/155188/trigger-a-button-click-with-javascript-on-the-enter-key-in-a-text-box
