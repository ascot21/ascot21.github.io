---
layout: post
title: "Rails Subscription Site with Recurly"
date: 2014-02-06 20:24:06 -0600
comments: true
categories: Rails, Gems
---

Creating a subscription based website with Rails can seem daunting at first, but with the help of [Recurly](http://recurly.com) and [Devise](https://github.com/plataformatec/devise) you can be up and running in no time.

Recurly.js does most of its work in the user's browser, dynamically building a form that handles the transaction.

**Note:** This tutorial assumes you've already setup Devise for authenticating your User model.

###Step 1 - Setup Your Recurly Account
Go to [https://app.recurly.com/signup](https://app.recurly.com/signup) and sign up for the Recurly service.

###Step 2 - Create a Subscription Plan
Once you've signed up for Recurly you'll be presented with a button to "Create a Subscription Plan".  Click that or if you are already in your account click on "Subscription Plans" under the "Configuration" section of the left navigation.  Then in the top right click "New Subscription Plan".
<!--more-->

Fill out the form.

- For the **Return URL after Success** enter ```http://localhost:3000/recurly/success?account_code={{account_code}}&plan={{plan_code}}``` (you'll want to change the root of this url when you push to production)
- check the **Bypass Recurly Confirmation?** checkbox
- note the **Plan Code** that you enter as you'll need it later in the config

###Step 3 - Enable API Access and Push Notifications
Recurly requires you to manually turn on access to the API. In Recurly, go to **API Credentials** under the **Developer** section of the left navigation.  Click **Enable API Access** and Recurly will generate an API Key and Access Key that you'll use later.

Next you'll need to enable push notifications.  Go to **Push Notifications** in the **Developer** section and then click **Configure**.  Fill out the form putting whatever username and password you'd like.  You'll have to use [RequestBin](http://requestb.in/) like Recurly suggests for the notification url during testing but can change this to you live server once you deploy to a production site.

###Step 4 - Install the Recurly Gem
To install the [Recurly gem](https://github.com/recurly/recurly-client-ruby) simply add ```gem 'recurly'``` to your Gemfile and then run ```bundle install```.

###Step 5 - Setup the Initializer
The Recurly gem includes a generator to create the initializer that your app will need.

Run ```rails g recurly:config``` to create the config/initializers/recurly.rb file.

It should look like:
{% codeblock lang:ruby config/initializers/recurly.rb %}
Recurly.subdomain        = ENV['RECURLY_SUBDOMAIN']
Recurly.api_key          = ENV['RECURLY_API_KEY']
Recurly.js.private_key   = ENV['RECURLY_JS_PRIVATE_KEY']
{% endcodeblock %}

###Step 6 - Set Environment Variables
You'll need to set the environment variables that Recurly uses in the config.  You can either do this manually or use a tool like [dotenv](https://github.com/bkeepers/dotenv) to manage them.

You'll need to set the following environment variables:

{% codeblock lang:bash %}
RECURLY_SUBDOMAIN # set during step three
RECURLY_API_KEY # set during step three
RECURLY_JS_PRIVATE_KEY # set during step three
RECURLY_PLAN_CODE # set during step two
RECURLY_USER # set during step three as 'HTTP Auth Username'
RECURLY_PASSWORD # set during step three as 'HTTP Auth Password'
{% endcodeblock %}

###Step 7 - Set the Recurly Global Settings
Recurly requires you to specify some general settings, specifically your subdomain and the currency you want to use.  To do so add the following to you application layout before the closing body tag.

{% codeblock lang:ruby views/layouts/application.html.erb %}
<script type="text/javascript">
 Recurly.config({
   subdomain: '<%= Recurly.subdomain %>',
   currency: 'USD'
 });
</script>
{% endcodeblock %}

###Step 8 - Configure Routes
You'll want to setup a few routes to handle the various parts of the signup process.

You'll need the following routes:

 - **/pay** - the page where the subscription payment form will be
 - **/thank-you** - the page that is rendered after a successful subscription
 - **/signatures/:plan_code** - a route for generating a signature to sign the Recurly requests
 - **/recurly/success** - the route Recurly will send back a success JSON response to

Add the following to your config/routes.rb file:
{% codeblock lang:ruby config/routes.rb %}
match '/pay' => 'site#pay'
match '/thank-you' => 'site#thank_you', :as => :thank_you
resources :signatures, :only => :show
resource :recurly, :only => [], :controller => :recurly do
  post :success, :on => :member
end
{% endcodeblock %}

###Step 9 - Setup the Signatures Controller
Recurly requires that you sign your request to make the data tamper proof.  The Javascript library includes a way to create this signature.

You'll need to create a Signatures controller.  You'll use this later to generate signatures for signing your requests.

{% codeblock lang:ruby app/controllers/signatures_controller.rb %}
class Api::V1::SignaturesController < Api::V1::BaseController
  def show
    respond_with({:signature => Recurly.js.sign(:subscription => { :plan_code => params[:id] })})
  end
end
{% endcodeblock %}

###Step 10 - Add Recurly Attributes to User Model
There are two attributes you'll need to add to your user model.  The **recurly_account_code** is the identifier Recurly uses to lookup an account.  The **subscription_active** attribute is a boolean that indicates whether or not that user has an active subscription with Recurly.

Create a migration by running ```rake db:migrate add_recurly_attributes_to_users```

Make sure the migration looks like:

{% codeblock lang:ruby %}
class AddRecurlyAttributesToUsers < ActiveRecord::Migration
  def change
    add_column :users, :recurly_account_code, :string
    add_column :users, :subscription_active, :boolean, :default => false
  end
end
{% endcodeblock %}

Run ```rake db:migrate``` to run the migration.

###Step 11 - Setup the Recurly Controller
The Recurly controller handles the success of a subscription request as well as push notifications from Recurly.

The success action grabs the Recurly subscription and saves the recurly_account_code to the User model.  In addition, it sets the subscription_active attribute (which you can use to determine if a user has an active subscription) to true.

The notification action handles push notifications from Recurly. It sets up some basic http authentication for the notification action. Recurly will send a push notification to you app any time a subscription is created or reactivated, thus updating the User model subscription_active attribute.  Expired subsciption push notifications from Recurly will mark the subscription_active attribute as false.

{% codeblock lang:ruby app/controllers/recurly_controller.rb %}
class RecurlyController < ApplicationController
  skip_before_filter :verify_authenticity_token
  http_basic_authenticate_with :name => ENV["RECURLY_USER"], :password => ENV["RECURLY_PASSWORD"], :only => :notification
  before_filter :authenticate_user!, :except => :notification

  def success
    subscription = Recurly.js.fetch params[:recurly_token]
    current_user.recurly_account_code = subscription.account.account_code
    current_user.subscription_active = true
    current_user.save
    redirect_to thank_you_path
  rescue Recurly::Error, NoMethodError => e
    logger.error e
    render :subscription_error
  end

  def notification
    case request.request_parameters.keys.first
    when "new_subscription_notification"
      activate_subscription params[:new_subscription_notification]
    when "reactivated_account_notification"
      activate_subscription params[:reactivated_account_notification]
    when "expired_subscription_notification"
      deactivate_subscription params[:expired_subscription_notification]
    end
    render :text => "OK", :status => 200
  end

  private
    def activate_subscription(nparams)
      user = User.find_by_recurly_account_code nparams[:account][:account_code]
      user.subscription_active = true
      user.save
    end

    def deactivate_subscription(nparams)
      user = User.find_by_recurly_account_code nparams[:account][:account_code]
      user.subscription_active = false
      user.save
    end
end
{% endcodeblock %}

###Step 12 - Setup Devise Redirect after Signup
By default the Devise gem redirects the user to the root_path after a successful signup.  We want the user to be redirected to a page where they can pay for the subscription so that the process is a seemless one.

Create a registrations_controller.rb file in a users directory.  This will redirect the user to the '/pay' url where our subscription payment form will sit.

{% codeblock lang:ruby app/controllers/users/registrations_controller.rb %}
class Users::RegistrationsController < Devise::RegistrationsController
  def after_sign_up_path_for(resource_or_scope)
    pay_url
  end
end
{% endcodeblock %}

You'll need to also specify the controller for the Devise route.

{% codeblock lang:ruby config/routes.rb %}
devise_for :users, :controllers => {:registrations => "users/registrations"}
{% endcodeblock %}

###Step 13 - Setup the Site Controller
We'll need to set the controller actions for the site_controller.  These actions don't do much.

{% codeblock lang:ruby app/controllers/site_controller.rb %}
class SiteController < ApplicationController

  def pay
  end

  def thank_you
  end

end

{% endcodeblock %}

###Step 14 - Create the Pay View
Create the markup for the pay_url.  Create the app/views/site/pay.html.erb file and add the following to it:

{% codeblock lang:erb app/views/site/pay.html.erb %}
<form id="recurly-payment-form"></form>
<%= javascript_include_tag 'recurly-payment' %>
<script type="text/javascript">
  CURRENT_USER = <%= raw current_user.to_json() %>;
  PLAN_CODE = '<%= ENV['RECURLY_PLAN_CODE'] %>'
</script>
{% endcodeblock %}

The form element is where the Recurly form will be generated by Recurly.js.  We also include a recurly-payment.js file and pass some variables to it.

###Step 15 - Create the Thank You View
This view can have whatever you'd like in it but make sure it includes a clear message that the subscription process is complete.

###Step 16 - Create the Javascript for Recurly.js
In this step you'll be writing the Javascript that will generate the subscription payment form.

The following code is in Coffeescript but you can adapt it to pure Javascript if you'd like.

{% codeblock lang:coffeescript app/assets/javascripts/recurly-payment.js.coffee %}
class RecurlyPayment
  constructor: (@data) ->
    @$form = $('#recurly-payment')
    @getSignature()

  getSignature: ->
    $.ajax
      url: "/signatures/#{PLAN_CODE}"
      type: 'GET'
      dataType: 'json'
      success: @handleSignatureAcquired

  handleSignatureAcquired: (response) =>
    Recurly.buildSubscriptionForm
      target: '#recurly-payment-form'
      planCode: PLAN_CODE
      successURL: '/recurly/success'
      signature: response.signature
      acceptPaypal: false
      acceptedCards: ["mastercard", "visa"]
      termsOfServiceURL: '/terms'
      billingInfo:
        firstName: CURRENT_USER.first_name
        lastName: CURRENT_USER.last_name
        address1: CURRENT_USER.address_1
        address2: CURRENT_USER.address_2
        country: 'United States'
        city: CURRENT_USER.city
        state: CURRENT_USER.state
        zip: CURRENT_USER.zip_code
      afterInject: =>
        @formLoaded()
    true

  formLoaded: ->
    # populate user info
    if CURRENT_USER
      @$form.find('.first_name :input').val(CURRENT_USER.first_name)
      @$form.find('.first_name .placeholder').hide()

      @$form.find('.last_name :input').val(CURRENT_USER.last_name)
      @$form.find('.last_name .placeholder').hide()

      @$form.find('.email :input').val(CURRENT_USER.email)
      @$form.find('.email .placeholder').hide()

$ ->
  paymentForm = new RecurlyPayment
{% endcodeblock %}

This will first hit the '/signatures' endpoint in your app to generate a Recurly signature and then using the response build the subscription form.

###Step 17 - Include Recurly.js in Your Application.js

Make sure you include the Recurly.js code in the asset pipeline.

{% codeblock lang:coffeescript app/assets/javascripts/application.js.coffee %}
#= require recurly
{% endcodeblock %}

###You're Done!

This concludes the basics of setting up a subscription payment form for a Rails app.  More than likely you'll want to also setup a form where a user can update their billing info.  To do so you'll need to use Recurly's [buildBillingInfoUpdateForm](https://docs.recurly.com/api/recurlyjs/reference#js-buildBillingInfoUpdateForm) function.
