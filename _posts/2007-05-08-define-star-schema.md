---
layout: post
title: Star Schema
---

I sent out [this link](http://enterprise.amd.com/Downloads/Bank_Leu_Case_Study_2_en.pdf) to some people just to peruse as an example of someone using SQL2005 as solution stack.

I received the following question back from someone.

> What is a one-star schema?  It is mentioned in the White Paper.

"What, indeed?", I said. I replied that it was a commonly used design schema used in data analysis and that I would have to refresh my memory so I could provide a much better answer.  Well, I have refreshed my memory and I thought I'd post my reply here.

Basically, a star schema is a way of organizing data in an analysis, or OLAP style database, It is a relational model. You have a fact table in the middle and dimension tables that all relate to it, thus creating a star. =) But, does everyone know what that means? What is a fact table? A dimension table? Well, let's define those as well.

A fact table is a conceptual type of table used in data analysis. Fact tables contain facts about your data that you want to analyze. If it were a sale, you would have records in your fact table that said something like 'I sold X product from X store on X date. Dimension tables may contain details about the dimensions. Perhaps a store dimension. Where is the store? How many employees does it have? What kind of store is it? Date/Time is always a popular dimension. Is it a holiday? What quarter? Which budget year? Is that sale on a Weekday? Of course, in this example, we have our product dimension. What kind of product? What color? How big? etc, etc, ad infinitum.

So, armed with that knowledge, perhaps it gives you a better understanding of what a star schema is. Here are some links I got on google that will give you some additional information:

http://www.bespoke.bm/pages/star.html (This is the best one I think. I found it while I was writing this one. =P)

http://perso.orange.fr/bernard.lupin/english/glossary.htm

http://www.itu.dk/people/pagh/DBS05/OLAP-MDM-2s.pdf

http://www.pcmag.com/encyclopedia_term/0,2542,t=star+schema&i=52005,00.asp

http://www.localcubetask.com/session1.htm

http://publib.boulder.ibm.com/infocenter/db2luw/v8/index.jsp?topic=/com.ibm.dwe.tutorial.doc/tutolaplescreatemd.htm

http://en.wikipedia.org/wiki/Fact_table (Check the other articles linked and explore the Data Warehousing keyword as well)