---
layout: post
title: List Fields/Formfields data in a Word Document using C# and Microsoft.Office.Interop.Word
---

I have a set of documents I need to review regularly. They are *mostly* form data in word so I wanted to write a simple script to extract the data I need from all of the documents and put it into a table. I wasn't sure how to do this so I wrote a little script to iterate over the data in the document. The documents I review have several versions, different form fields, and other irregularities. I wanted to save a little script I wrote that uses various methods to iterate through the document. I use LINQPad regularly, so I wrote this in there in C# as a method for opening and handling the word document, and an extension for iterating it.

{% gist a74375d367f90f3a3214 %}

References:

- http://stackoverflow.com/questions/6777422/disposing-of-microsoft-office-interop-word-application
- http://stackoverflow.com/questions/1976503/iterating-over-word-document-fields-using-comautomationfactory-in-silverlight-4