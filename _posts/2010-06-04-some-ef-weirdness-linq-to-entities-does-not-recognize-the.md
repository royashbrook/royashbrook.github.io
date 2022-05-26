---
layout: post
title: Some EF Weirdness (LINQ to Entities does not recognize the method 'System.String ToString()')
---

So I was writing a really simple EF web method today. It just checked for the presence of two values in a table and would return true or false.

```csharp
[WebMethod]
public bool weird(long l, string s) {
    using (someEntities db = new someEntities())
       return db.things.SingleOrDefault(x => x.s == s && x.l == l.ToString()) != null;
}
```

The code above gives me the following error:

`System.NotSupportedException: LINQ to Entities does not recognize the method 'System.String ToString()' method, and this method cannot be translated into a store expression`

This error gets thrown on the l.ToString(). It apparently has to do with LINQ expressions being evaluated somewhere else other than where it is built. Makes sense since ToString() could be anything as far as LINQ is concerned. I would think that things like string and int would be kinda handled, but hey, it's no biggy.Very simple work around, you just have to actually pass it a string in your LINQ expression like so just make a string and pass it in like so:

```csharp
[WebMethod]
public bool canAccessCustomer(long l, string s) {
    string sl = l.ToString();
    using (someEntities db = new someEntities())
        return db.things.SingleOrDefault(x => x.s == s && x.l == sl) != null;
}
```

One could argue about taking in a long that is stored as a string and why it's not a long or some equivalent number in the db, but sometimes things are the way they are. =P

I found a couple of different pages referencing this one, but this seemed to be the best although it had a bunch of other 'solutions' other than simply passing a string in instead of running the function in your LINQ query which seemed the simplest to me.