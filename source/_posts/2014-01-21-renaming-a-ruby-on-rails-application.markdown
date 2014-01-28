---
layout: post
title: "Renaming a Ruby On Rails Application"
date: 2014-01-21 21:32:48 -0600
comments: true
categories: [Ruby on Rails, gems]
---

Yes, renaming a Rails application isn't something that you'll do often.  But when you do have to do it, you'll quickly realize that it's not as easy as just changing the name in a file or two. Your application name is spread out through a few dozen files including environment configs, initializers, routes and our application.rb file.

Luckily you don't have to remember which files to change.  Morshed Alam created a gem (appropriately named [Rename](https://github.com/morshedalam/rename)) that handles the grunt work for you.  Rename goes through your app and performs a ```gsub``` where appropriate.

###To Rename Your Application

Add this line to your Gemfile:
``` ruby
gem "rename"
```
<!--more-->

Rename the app:
```
rails g rename:app_to NEW_NAME
```

This will rename the app in all of these files:

```
config.ru
Gemfile
Gemfile.lock
Rakefile
README.rdoc
config/application.rb
config/boot.rb
config/environment.rb
config/environments/development.rb
config/environments/production.rb
config/environments/test.rb
config/initializers/backtrace_silencers.rb
config/initializers/filter_parameter_logging.rb
config/initializers/inflections.rb
config/initializers/mime_types.rb
config/initializers/secret_token.rb
config/initializers/session_store.rb
config/initializers/wrap_parameters.rb
config/routes.rb
config/initializers/session_store.rb
```

You'll probably want to rename your databases while you are at it.

For Postgresql, log into psql and then:
```
ALTER DATABASE db_name RENAME TO new_db_name;
```

Enjoy!