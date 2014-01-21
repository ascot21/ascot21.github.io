---
layout: post
title: "Learning Ruby/Ruby on Rails:  A Guide From A Fellow Nuby"
date: 2012-12-3 10:00:23 -0600
comments: true
categories: [Ruby, Ruby on Rails, Beginner]
---

2012 has been a transformative year for my career.  Back in May I heard about a new software training program called the Nashville Software School.  It's a new innovative training program that teaches students the fundamentals of web application technologies in via classroom lectures, exercises, mentors and apprenticeship.  I got involved with the program by being a teacher for the front-end part of the school.  I was a student in the second half which taught us the fundamentals of web application development with Ruby on Rails and all of the associated technologies.  After going through the program, I'd like to share my tips on how to learn Ruby and Rails.  There is more that one way to learn but here's what worked for me.

<!--more-->

###Prerequisites

***HTML/CSS*** - You don't need to be an expert but it'll come in handy once you start working on views in Ruby on Rails

***Unix Command*** Line- Again you don't need to be an expert but you need to now how to navigate through directories, manipulate files and alter file permissions.

***Text Editor*** - While you can certainly get started with a basic text editor like Notepad or TextEdit, starting out with a real editor like Sublime Text 2 or Textmate will make you more productive in the end.  These editors have features like syntax highlighting, code completion and snippets.  Plus they are extensible so that you can add features and customize them to your liking.


###Learn Pure Ruby First

A lot of people come to Ruby by first going straight into Rails without first learning the core Ruby language.  I think this is a mistake and will slow you down when picking up the language.  If you jump right into Rails, it will be hard to decipher what is Rails and what is actually just Ruby.

I recommend picking up a beginner book such as Beginning Ruby and diving in.  That book in particular is focused on beginners so it only covers the fundamentals but it will get you going pretty quickly.

Also, there are several great online interactive learning programs you can do.  I recommend [Ruby Monk](https://rubymonk.com/), [Ruby Koans](http://rubykoans.com/) and [Codecademy](http://www.codecademy.com/).

###Learn Basic Data Structures

Learning about the various data structures that are used in software development before diving into Rails will pay off.  One of the trickiest parts of building an app is deciding how it will work.  Knowing what data structures are out there and what the best practices for certain problems will allow you to architect better solutions.

Learn the primitive typs such as boolean, floats and enumerables.  Learn the more advanced data structures such as arrays, maps, linked-lists, binary trees and queues.

Once you think you've got the major data structures memorized, write a program for a linked-list.  Once you have that write a doubly-linked-list.  For more check out the Wikipedia article [Data Structures](http://en.wikipedia.org/wiki/Data_structure)

###Version Control Is Your Friend

It happens to everyone.  You spend hours and hours writing some complex code.  Then your hard drive crashes or you accidentally delete a file.  All of that hard work is gone. But if you would have learned version control you could be back up running in a matter of minutes.

So learn [Git](http://git-scm.com/) and how to use [Github](http://github.com/) as your remote repository for all of your code.  I recommend starting out by following along with the tutorial on [Git+Immersion](http://gitimmersion.com/).  It'll walk you through getting Git setup on your system and the basics of creating repos, recording or committing code, working with branches and recovering files.  The [Git Book](http://git-scm.com/book) is another great read.

Finally, I'd like to point out that using Git and Github not only allow you to have version control but they also are a huge tool for collaborating with other developers.  They allow multiple devs to work on the same code and then later seamlessly merge them together with little effort.  Github is also the central place that open-source Ruby projects are managed.  Many of the tools and corresponding documentation that you'll use once you get to Rails are hosted there.  Get comfortable with it now.

###Test-Driven Development Makes You And Your App Better

Writing the tests for your code before writing the code itself might sound a little backwards but trust me, it'll lead to better code.  By taking the time to lay out your tests you'll be knocking out more than you realize.  Not only will the tests check your code to make sure it works like you intended but it will also force you into thinking through how you want it to work (something that devs often don't fully do).  Often the process of writing the tests will help you simplify the problem and help you find easier solutions.  The biggest benefit of TDD is that you'll gain confidence in your code.  Adding features to a large code base is no longer scary.  If you have tests, you'll know of any bugs before you ship.

Rails ships with Test::Unit but most developers are using [RSpec](http://rspec.info/).  The official documentation is a good place to start.  Also many developers are using [Cucumber](http://cukes.info/) to write behavior driven tests.

Learning TDD is hard.  It requires a mindset change for most people.  But from talking with lots of seasoned developers, it's a battle worth waging.  Dig in and learn to love it.  Once you do you'll never look back.  The confidence in your code that you gain is worth it.  Not to mention the money saved in not having to chase bugs.

###Learn the Ruby on Rails Framework

Now that you've got the fundamentals of Ruby, version control and TDD down, you can jump into Rails.  Rails is a huge monolithic framework that can be intimidating at first.  But once you have the fundamentals of the software down, the details of various parts are easier to work out.

Here's the order that I suggest you learn the main parts:

1. Active Record Associations
2. Rails Database Migrations
3. Query Interface
4. Action Controller
5. Routing
6. Views and Layouts
7. Form Helpers

David Hartl's free online book "[Ruby on Rails Tutorial](http://ruby.railstutorial.org/ruby-on-rails-tutorial-book)" is a great introduction to Rails.  It covers all of the basics without being to intimidating.  Next I recommend checking out the official [Ruby on Rails Guides](http://guides.rubyonrails.org/).  The guides layout the most basic features in a well thought out manner with lots of examples.  This proved to be my favorite resource for the basics.  Also, the [official docs](http://api.rubyonrails.org/) are well thought out and include everything you need.

Once you have those basics down, I suggest diving deeper by learning:

- Action Mailer
- Debugging
- Performance Testing
- Command line tools and rake tasks
- Security

Finally, as we discussed earlier it's important to learn how to test Rails applications as you build them.  Rails ships with Test::Unit but most people also use RSpec for testing.  Some even go the extra step and use Cucumber to do integration testing.  I recommend following along with the tutorial in [Rails 3 In Action](http://www.manning.com/katz/). The process that the author uses is TDD.  You'll learn lots of ways of testing the different parts of the application.

###Don't Stop Learning

Just like most other technologies the feature-set and syntax of Ruby on Rails is continuously evolving.  It's important to stay up to date with the latest techniques and features.  Here's my recommendations where to look for new info.

[Ruby Weekly](http://rubyweekly.com/) - a free weekly newsletter with links to the newest articles around the web

[Ruby Inside](http://www.rubyinside.com/) - the latest news and tutorials

[Rails Casts](http://railscasts.com) - screencast tutorials. My single favorite resource.

[Ruby-Toolbox](https://www.ruby-toolbox.com/) - gem library with lots of useful information

[Ruby Flow](http://www.rubyflow.com/) - updated daily with every news story related to Ruby

[Ruby Rogues](http://rubyrogues.com/) - weekly podcast featuring some of the communities top developers