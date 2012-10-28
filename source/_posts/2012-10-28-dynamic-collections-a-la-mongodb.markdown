---
layout: post
title: "Dynamic collections a la MongoDB, but with MySQL"
date: 2012-10-28 16:52
comments: true
categories: mongodb mysql nosql
keywords: mongodb mysql nosql dynamic
description: how to create mysql collections dynamically, like in mongodb 
---

After having worked with MongoDB for a while, I really miss its dynamic features when 
dealing with "legacy" systems (MySQL, in this instance). How cool it would be to just start
inserting data into random collection and it will magically appear in the DB? **Good news is**: 
it can be done.

<!-- more -->

There are some tradeoffs to be made, however. If we want completely flexible row-per-row
structure, we have to serialize our data into JSON blobs (or whatever). This way we lose
ability to efficiently query the data. Fortunately, in many cases there *is* a structure in 
data, so it can be represented as a MySQL schema.

In this concrete example we are storing event stream. For the ease of management we decided 
to split the stream by source app id and date (in this app we don't have to query across
several apps or days at the same time). So, our table names should follow this pattern, 
where `datestr` is a date formatted as `yyyymmdd`

``` ruby
"#{app_id}_daily_events_#{datestr}"
```

So, how do we do it? We utilize [API for schema manipulations](http://api.rubyonrails.org/classes/ActiveRecord/ConnectionAdapters/SchemaStatements.html),
which is conveniently provided to us by ActiveRecord. We are interested in the [create_table](http://api.rubyonrails.org/classes/ActiveRecord/ConnectionAdapters/SchemaStatements.html#method-i-create_table) method.

``` ruby
# create_table() passes a TableDefinition object to the block.
# This form will not only create the table, but also columns for the
# table.

create_table(:suppliers) do |t|
  t.column :name, :string, :limit => 60
  # Other fields here
end
```

Here's how our method definition might look like:
``` ruby
# Calculates and sets current table name, based on passed parameters. Example:
#
#   SqlDailyEvents.use_table_for_app 62, Time.now
#
# @param aid Application ID
# @param date [Time] Time of event
# @return [String] Calculated table name
def self.use_table_for_app aid, date, params = {}
  name = make_table_name(aid, date)
  self.table_name = name

  unless table_exists?
    connection.create_table name do |t|
      t.column :event_id, :integer, :null => false
      t.column :event_value, :integer
      t.column :user_id, :string, :null => false, :limit => 30
    end
    clear_cache!
    
  end

  table_name
end

def self.make_table_name app_id, event_date
  datestr = event_date.strftime '%Y%m%d'
  "#{app_id}_daily_events_#{datestr}"
end
```

Note the `clear_cache!` call. We need to invalidate ActiveRecord's internal caches, or 
otherwise it won't know about our newly created table until app is restarted. Writing a 
reverse method is pretty straightforward:
``` ruby
# Drops corresponding table
#
# @param aid Application ID
# @param date [Time] Time for which table should be dropped
def self.forget_table_for_app aid, date
  name = make_table_name(aid, date)

  if ActiveRecord::Base.connection.table_exists?(name)
    connection.drop_table name

    clear_cache!
  end
end
```

And this is how you use the code:
``` ruby
SqlDailyEvents.use_table_for_app aid, cur_time

SqlDailyEvents.create event_id: evid,
                      event_value: evval,
                      user_id: uid

```

That is, prefix every `create` call with a `use_table_for_app` call, so that current
table is properly set and created (if needed). I admit, this is still far from being
completely transparent, but this works for me at the moment. Feedback is welcome! 

Happy coding!