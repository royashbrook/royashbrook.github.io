---
layout: post
title: for > foreach
---

i feel like i have seen a ton of places that say for and foreach are the same performance-wise in .net. i never really worried about it much. i have historically done a lot of refactoring and loops are definitely one area you can see lots of improvement in. so i figured i would post some actual documentation about this. 😀

 quoted from http://msdn2.microsoft.com/en-us/library/ms998574.aspx:

 > Using foreach can result in extra overhead because of the way enumeration is implemented in .NET Framework collections. .NET Framework 1.1 collections provide an enumerator for the foreach statement to use by overriding the IEnumerable.GetEnumerator. This approach is suboptimal because it introduces both managed heap and virtual function overhead associated with foreach on simple collection types. This can be a significant factor in performance-sensitive regions of your application. If you are developing a custom collection for your custom type, consider the following guidelines while implementing IEnumerable:

> If you implement IEnumerable.GetEnumerator, also implement a nonvirtual GetEnumerator method. Your class's IEnumerable.GetEnumerator method should call this nonvirtual method, which should return a nested public enumerator struct.
Explicitly implement the IEnumerator.Current property on your enumerator struct.
For more information about implementing custom collections and about how to implement IEnumerable as efficiently as possible, see "Collection Guidelines" in Chapter 5, "Improving Managed Code Performance."

> Consider using a for loop instead of foreach to increase performance for iterating through .NET Framework collections that can be indexed with an integer.

so, there you have it. use for to get the greatest performance. 😀
