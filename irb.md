# Using your Rails Console #

These are some beginner tips that might help you get started with irb and the ails console.

### Getting Started ###
1. Open you command prompt and `cd path/to/my/app`
2. type `rails console` and press enter

This opens an instance of irb with all your Rails app goodies.
Be sure to back up your database before proceeding. The easiest way is to `mv db/development.sqlite3 db/development.sqlite3.backup` or similar.

### Viewing your tables ###
You can view your data directly through the console. Say I have an Events model. Just like in my events_controller, I can grab all my events in the console by typing `Event.find(:all)`. Assign it to a variable like `@events`.
      > @events = Event.find(:all)
That was easy enough. But the console might spit out a bunch of unformatted data at you. To make things easier to read type
      > y @events
y is short for yaml (the cutest markup lanquage, sorry markdown). Think of it like an object's `puts` function. It displays your data with fancy newlines and indentation. Neat huh?

### Let's Get Creative ###
Let's keep going with this Event model thing. Some of our data fields might be `:title`, or a datetime `start_at`.
We can manually create an event by typing
      > my_birthday = Event.new( :title => 'Alex\'s Birthday', :starts_at => 'April 29, 1988, 11:45PM' )
let's check out `my_birthday`
      > y my_birthday
Neat! Let's save it to our database.
      > my_birthday.save
Easy enough. You can save a step by using the `create` method instead
      > Event.create( :title => 'Alex\'s Birthday', :starts_at => 'April 29, 1988, 11:45PM' )
Isn't Rails fantastic?

### Let's Get Destructive ###
I hope you backed up your database. Type
        > Event.destroy_all # to kill all your events
        > my_event.destroy # to kill a specific event

### Useful Links ###
* http://www.ruby-doc.org/
* http://api.rubyonrails.org/classes/ActiveRecord/Base.html
* http://github.com/ebcomalex # my github account