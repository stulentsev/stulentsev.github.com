---
layout: post
title: "Limitations of MongoDB"
date: 2014-02-25 13:18
comments: true
categories: mongodb
---

MongoDB is becoming even more popular than it is now. More people want to learn about it. So I was preparing a seminar for this company and I had to compile a list of MongoDB limits. I never knew there were so many! Some of them are reasonable, some are weird. Anyway, it's good to know them. Here's a list of MongoDB limits as of version 2.4.9:

* Max document size: 16 MB (we all knew this one, right?)
* Max document nesting level: 100 (documents inside documents inside documents...)
* Namespace is limited to ~123 chars (namespace is db_name + collection_name (or index_name))
* DB name is limited to 64 chars
* Default .ns file can store about 24000 namespaces (again, a namespace is referring to a collection or an index)
* If you index some field, that field can't contain more than 1024 bytes
* Max 64 indexes per collection
* Max 31 fields in a compound index
* fulltext search and geo indexes are mutually exclusive (you can't use both in the same query)
* If you set a limit of documents in a capped collection, this limit can't be more than 2**32. Otherwise, number of documents is unlimited.
* On linux, one mongod instance can't store more than 64 TB of data (128 TB without journal)
* On windows, mongod can't store more than 4 TB of data (8 TB without journal)
* Max 12 nodes in a replica set
* Max 7 voting nodes in a replica set
* You can't automatically rollback more than 300 MB of data. If you have more than this, manual invervention is needed.
* `group` command doesn't work in sharded cluster.
* db.eval() doesn't work on sharded collections. Works on unsharded, though.
* `$isolated`, `$snapshot`, geoSearch don't work in a sharded cluster.
* You can't refer `db` object in `$where` functions.
* If you want to shard a collection, it must be smaller than 256 GB, or else it will likely fail to shard.
* Individual (not multi) updates/removes in a sharded cluster must include shard key. Multi versions of these commands may not include shard key.
* Max 512 bytes for shard key values
* You can't change shard key for a collection once it's sharded.
* You can't change value of a shard key of a document.
* aggregate/$sort produces error if sorting takes more than 10 percent of RAM.
* You can't use $or in 2d geo queries
* You better not use queries with multiple $in parts. If this results in more than 4 million combinations - you get error.
* Database names are case-sensitive (even on case-insensitive file systems)
* Forbidden characters in database names: linux - /\. ", windows - /\. "*<>:|?
* Forbidden characters in collection names: $ sign, "system." prefix
* Forbidden characters in field names: .$
* Hashed index can't be unique
* Max connection number is hardcoded to 20k.


Hope this was useful to you. 
