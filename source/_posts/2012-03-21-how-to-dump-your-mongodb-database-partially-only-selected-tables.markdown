---
comments: true
date: 2012-03-21 15:15:22
layout: post
slug: how-to-dump-your-mongodb-database-partially-only-selected-tables
title: How to dump your MongoDB database partially (only selected tables)
wordpress_id: 335
tags:
- bash
- MongoDb
---

Let's say you want to dump your MongoDB database. There's a handy tool that does just that, **mongodump**.




    
    
    mongodump
    





If executes without any arguments it will try to connect to localhost:27017 and dump all databases. You can specify a single database that you're interested in and it will dump just this database.




    
    
    mongodump -d mydb
    





But in some cases you don't want to dump the whole database. In my case, it's an analytics application and 99% of data is in raw event collections (**events20120320**, **events20120321**, ...). I am interested only in a small number of "important" collections. But **mongodump** doesn't provide us with an option to specify several collections. You can only dump one collection at a time. If you don't mind some typing, that's easy.




    
    
    mongodump -d mydb -c mycoll1
    mongodump -d mydb -c mycoll2
    mongodump -d mydb -c mycoll5
    





But we're all programmers, so let's automate this stuff. I, personally, never used bash loops before, and it seems like a good use case for them. Let's do it.




    
    
    colls=( mycoll1 mycoll2 mycoll5 )
    
    for c in ${colls[@]}
    do
      mongodump -d mydb -c $c
    done
    





First line defines a bash array literal. **Don't use commas to delimit array elements**, they'll become part of the element. The **${colls[@]}** string means "all array elements" and it can be used anywhere where a variable is expected. The rest is pretty straightforward, I think.



