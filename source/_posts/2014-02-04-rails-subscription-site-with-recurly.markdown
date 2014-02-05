---
layout: post
title: "Rails Subscription Site with Recurly"
date: 2014-02-04 20:24:06 -0600
comments: true
categories: Rails, Gems
published: false
---

Creating a subscription based website with Rails can seem daunting at first, but with the help of [Recurly](http://recurly.com) you can be up and running in no time.

Steps:
1. [Sign up for Recurly](https://app.recurly.com/signup)
2. Configure a subscription plan
3. Add gem to gemfile
4. Generate Recurly initializer ```rails g recurly:config```
5. add config script to application layout
6. setup routes
7. Create signatures controller
8. Create Recurly controller
9. Add pay and thank_you actions to site_controller
10. redirect after_sign_up_path devise
11. setup Pay view
12. setup thank_you view
13. add recurly attributes to user model
14. recurly-javascript.js
15. include recurly.js in application.js


###Step One - Setup Your Recurly Account
Go to [https://app.recurly.com/signup](https://app.recurly.com/signup) and sign up for the Recurly service.

###Step Two - Create a Subscription Plan
Once you've signed up for Recurly you'll be presented with a button to "Create a Subscription Plan".  Click that or if you are already in your account click on "Subscription Plans" under the "Configuration" section of the left navigation.  Then in the top right click "New Subscription Plan".

Fill out the form.

- For the **Return URL after Success** enter ```http://localhost:3000/recurly/success?account_code={{account_code}}&plan={{plan_code}}``` (you'll want to change the root of this url when you push to production)
- check the **Bypass Recurly Confirmation?** checkbox
- note the **Plan Code** that you enter as you'll need it later in the config

###Step Three - Enable API Access
Recurly requires you to manually turn on access to the API. In Recurly, go to **API Credentials** under the **Developer** section of the left navigation.  Click **Enable API Access** and Recurly will generate an API Key and Access Key that you'll use later.

###Step Four - Install the Recurly Gem
To install the [Recurly gem](https://github.com/recurly/recurly-client-ruby) simply add ```gem 'recurly'``` to your Gemfile and then run ```bundle install```.

###Step Five - Setup the Initializer
The Recurly gem includes a generator to create the initializer that your app will need.

Run ```rails g recurly:config``` to create the config/initializers/recurly.rb file.

It should look like:
{% codeblock lang:ruby config/initializers/recurly.rb %}
Recurly.subdomain        = ENV['RECURLY_SUBDOMAIN']
Recurly.api_key          = ENV['RECURLY_API_KEY']
Recurly.js.private_key   = ENV['RECURLY_JS_PRIVATE_KEY']
{% endcodeblock %}

###Step Six - Set Environment Variables
You'll need to set the environment variables that Recurly uses in the config.  You can either do this manually or use a tool like [dotenv](https://github.com/bkeepers/dotenv) to manage them.

You'll need to set the following environment variables:

{% codeblock lang:bash %}
RECURLY_SUBDOMAIN # set during step three
RECURLY_API_KEY # set during step three
RECURLY_JS_PRIVATE_KEY # set during step three
RECURLY_PLAN_CODE # set during step two
{% endcodeblock %}

###Step Seven - Set the Recurly Global Settings
Recurly requires you to specify some general settings, specifically your subdomain and the currency you want to use.  To do so add the following to you application layout before the closing body tag.

{% codeblock lang:ruby views/layouts/application.rb %}
<script type="text/javascript">
 Recurly.config({
   subdomain: '<%= Recurly.subdomain %>',
   currency: 'USD'
 });
</script>
{% endcodeblock %}

