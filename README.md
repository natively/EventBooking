# Doing things the right way with Rails and Heroku

## Tools used:
### Ruby 1.9.2
* Beautiful, easy-to-read syntax
* Vast standard library
* RubyGems, the easiest and best way to manage add-on libraries

### Rails 3.1
* Emphasis on rapid prototyping and development cycle
* Separation of logic using the Model-View-Controller pattern
* "Convention over Configuration"
* Test Driven Development using RSpec
* Uses git, not subversion as a standard

### Heroku
* http://heroku.com
* Ease of deployment
* Easily create and manage staging and production data
* Handy command line interface on Heroku's Cedar stack
* Rock-solid hosting via Amazon cloud
* Scales nicely based on server load

### Git
* distributed version control
* repository on Github
* http://help.github.com/

#### Generic git workflow:
    git checkout -b name_of_my_branch
    # make changes here
    git status (see changes)
    git add <name_of_file> (or "git add ." for everything)
    git commit -m "Detailed message about the changes"
    git checkout master
    git merge name_of_my_branch
    git push origin master

### Sass
* http://sass-lang.com
* `gem install sass`
* Run the observer script in the background. Use `sass --watch myfile.scss:myfile.css`

## RefineryCMS and EventBooking
* http://refinerycms.com
* easy CMS to learn
* extendible and customizable using "engines"
* stacks on top of a normal Rails app without being obtrusive, adheres to "The Rails Way"
* see http://refinerycms.com/guides for tutorials on adding engines to your app

### The Events Engine
The events engine is just begging to be hacked.  Here's what I did for Montego Bay:

1. Add a migration to the Events table that adds a unique_event_id field. It might look like this:
        
        class AddUniqueEventIdToEvents < ActiveRecord::Migration
          def self.up
            add_column :events, :unique_event_id, :integer
            add_index :events, :unique_event_id, :unique => true
          end
          
          def self.down
            remove_column :events, :unique_event_id
          end
        end

2. run `rake db:migrate` to update your table
3. run `rake refinery:override model=Event` and add the line `validates_uniqueness_of :unique_event_id` to app/models/event.rb
4. Here's where the fun begins.  Set up a Cron add-on in your Heroku app. Then write a script to pull in data from the Eventbooking XML streams and write it to the Events table. Save it to lib/tasks/cron.rake and run it manually with `rake cron` (or `heroku run rake cron` to test it). This was my first draft

        desc 'this task populates our event table from eb-xml streams'
        task :cron => :environment do
          require 'nokogiri'
          require 'open-uri'
          # make this the url of your EventListXML stream
          url = "http://go-xml.smgbooking.com/EventListXml.ashx?wl=3.7FEDB662"
          Nokogiri::XML(open(url)).xpath("//event").each do |e|
            
            puts "Creating " + (e>"event_name").text + "..."
    
            # get the date (for readability)
            date_range = e>"date_range"
            start_date = DateTime.parse( (date_range>"start_date").text + 
                                         (date_range>"start_time").text )
            end_date = DateTime.parse( (date_range>"end_date").text +
                                       (date_range>"end_time").text )
    
    
            Event.create(
              :title => (e>"event_name").text,
              :unique_event_id => (e>"unique_event_id").text.to_i,
              :description => (e>"description").text ,
              :start_at => start_date,
              :end_at => end_date )
          end
        end


### The CopyWriting Engine
Adding the copywriting engine to your app will let users control snippets of text in your page. Here's an example of copywriting code I embedded in the footer, which was scanned and added to the backend admin automatically.

    <li>
      Phone: <%= copywriting('phone number', { :scope => 'footer', :default => '1.800.867.5309' }) %>
    </li>

Pretty easy huh!?

## An aside
My favorite editor is vi, but vi kinda sucks on windows so I instead use Sublime Text 2.  Coupled with Git Bash and Command Prompt, it is possible to develop Rails applications on Windows and still have a nice envrionment.