---
layout: post
title:      "CLI Data Gem Project: I DID THAT."
date:       2018-07-29 15:24:19 -0400
permalink:  cli_data_gem_project_i_did_that
---


I'm not going to lie, just thinking about this project had me terrified before I even started. It's the first time I was going to be on my own with a blank canvas. I would have to write each and every line of code myself to build an entire application -- one that actually works. No starter code, Flatiron? Not even a bit? Are you SURE?

***They're sure.***

And for good reason. I learned so much having to build this app on my own. 


I wanted to build an app about something that I'm actually interested in, and since I love traveling and especially loved my  visits to Vancouver, I originally chose the website for Capilano Suspension Bridge Park. After spending a ridiculous amount of time trying to scrape that website, I met with a technical coach who said "whoever built this site really just didn't care." He was right. I didn't realize it when I chose the site, but it wasn't exactly the best for scraping and I decided to start a new project using a different website. Now I could say my time spent on the failed Cap Bridge project was a waste (and trust me, it was a LOT of time), but I think I actually learned a lot while struggling with it. When I switched to a new site, things came much more easily to me because I went over the same concepts (except in an unnecessarily complicated way) in my first project attempt. This brings me to my ACTUAL project...a gem called ["top_ten,"](https://github.com/alavia/top_ten) which teaches the user about Lonely Planet's Best in Travel 2018 Top 10 Countries to Visit. 

Here's how it all went down...

I started building this application by using bundler (```bundle gem top_ten```) to stub out the general structure of a standard gem without having to do it all manually. A lifesafer for someone who got used to having some starter code. It was the closest thing I was going to get. I also wrote myself a brief note of what I wanted my gem to do and how I wanted it to work:

* welcome user
* display list of countries
* ask user for country number and accept input
* show details of selected country
* list again or exit
* goodbye if exit

Then I created my executable file (```bin/top_ten```), which lives in the bin directory. This file needs the following "shebang" line so that it's interpreted as a Ruby file:

`#!/usr/bin/env ruby`

After that, I had to add executable permission in order for the file to, well, *execute*. At first my new bin file only had read and write permissions, but by running `chmod +x top_ten`, I was able to add executable permission as well.

Next, I created my lib files where most of my code was going to live. First, I made `lib/top_ten.rb`, which was my environment and would contain all of my requirements. I also created three files which were each classes in my application: `cli.rb`, `scraper.rb`, and `top.rb` all of which were in my `lib/top_ten` directory. I added these classes as requirements in my environment. In the CLI class, I created a method called "call" and added a simple `puts "Hello World"` in it for the time being. Then in my bin file, I required my environment `lib/top_ten.rb` and called the CLI "call" method (`TopTen::CLI.new.call`), making sure it was printing "Hello World" and properly creating a new instance of the CLI upon execution. All of this set up the load order of the files, which is as follows: 

1. `bin/top_ten` - execution point. Instantiates a new instance of the CLI and calls the "call" method in the CLI class.
2. `lib/top_ten.rb` - required by bin file. Is the program environment. Contains requirements.

Understanding all of that was one my earliest hurdles in this project. I was so relieved once it all clicked!

Once the environment and requirements were all up and running and as I continued to code, I added dependencies to my gemspec file (`top_ten.gemspec`). I first started out by adding bundler and rake as development dependencies and added pry soon afterwards. Once I got to building my scraper, I added Nokogiri as a dependency as well.

Building my CLI class was next, and I coded a fairly simple one that could easily be tweaked as I worked on my scraper. I hardcoded a "list_countries" method that displayed a list of countries. I also created a hardcoded "menu" method that asked for user input and behaved accordingly using case statement logic (which I later changed to if/else logic). Although I didn't build a "welcome" method until later on in my coding process, I did create a "goodbye" method at that time. As I countinued building my application, I replaced the hardcoded portions of the CLI -- first with hardcoded data in my scraper, and then with actual scraped data. Next up was my scraper class.

Before building my scraper class, I decided to write down my code in a notebook where I was taking notes about my project. I actually wrote out my entire scraper class before the rest of my project, because scrapers were fresh in my mind after my many attempts at my original failed Cap Bridge project. Before settling on LonelyPlanet.com, I was inspecting the site's code to make sure I didn't land on another site that would give me trouble. Turns out Lonely Planet's developers actually DO care! The site's code looked really clean to me and turned out to be easy to scrape and allowed me to build a simple scraper class. At first, I decided not to use my original plan and hardcoded the scraper by following Avi's video. I had some trouble getting it to work when I started using actual scraped data, so I tried the method I came up with before. Here's what I ended up with, which is almost exactly what I wrote down in my little notebook before starting the project (and yeah, I'm really proud of that. Not sure if I should be, but I am):


```
class TopTen::Scraper

    def self.scrape_country
        doc = Nokogiri::HTML(open("https://www.lonelyplanet.com/best-in-travel/countries"))
        countries = doc.css(".marketing-article")
        countries.collect do |country|
            new_country = TopTen::Top.new
            new_country.name = country.css("h1").text
            new_country.description = country.css(".marketing-article__content").text.strip

            new_country.save
        end
    end
end
```


This scraper first creates a local variable called `doc` which is all of the HTML on the particular Lonely Planet page that I chose to scrape. Then it takes `doc` and uses a CSS selector to further drill down into the data and sets that equal to a new variable called `countries`. After that, `countries` is iterated over using `.collect` to collect information about each country on the site. In the block, a new instance of the Top class is created and called `new_country`. Then, using CSS selectors, the name and description of each country is scraped. Finally, the `.save` method adds the `new_country` object to the `@@all` array. `@@all` is defined in the Top class, as well as attr_accessors (setter and getter methods) for name and description:


```
class TopTen::Top
    attr_accessor :name, :description
    @@all = []

    def self.all
        @@all
    end

    def save
        @@all << self
    end
		
end
```

I first thought I could add each new country to `@@all` by using `@@all << new_country`, but I realized that since `@@all` was a class variable, it would not be accessible by any class besides the Top class.

Once my scraper and Top class were done, I made some adjustments to my CLI to make sure it worked seamlessly with the other two classes and was exactly how I wanted it. And then...voilà! After writing all of my code and testing and debugging it over and over again...everything was working! I found the light at the end of my CLI project tunnel, and came out with a much better understanding of object oriented Ruby, scraping, and how a CLI works. 

Here's a look at my gem in action!

When top_ten is executed, the user is greeted and given a list of Lonely Planet's Best in Travel 2018 Top Ten Countries to Visit:

![](https://i.imgur.com/SCQXOuU.png)

The user can then enter a number for the country they would like to learn more about, or enter "list" to see the list again:

![](https://i.imgur.com/udnJTr8.png)

If the user types "exit," the program will say goodbye and exit:

![](https://i.imgur.com/Eib4hSg.png)



Running my application and watching it do just what it was built to do is such a great feeling of accomplishment. I'm proud to say [I DID THAT](https://github.com/alavia/top_ten).



