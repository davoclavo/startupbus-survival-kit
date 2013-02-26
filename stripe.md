Stripe
======

Set up SSL Certificate
----------------------

Heroku - $20/month + Certificate - [https://devcenter.heroku.com/articles/ssl](https://devcenter.heroku.com/articles/ssl)


Set up [Stripe](http://stripe.com) account
---------------------------------------------
 1. Go to **Your Account** menu in the top right and open the *Account Settings* pane
 2. Set a good **Statement Descriptor**. This is what customers will see on their credit card statements.
    - *A good descriptor helps the customer remember what they bought so that they don’t mistake your transaction for fraud and cancel the charge.* (When this happens, it’s called a “chargeback” and you’ll pay a $15 fee on top of losing the sale, so make sure your descriptor is set!)
 3. Specify the bank account to which you’d like your money deposited.
 4. Grab the *API Keys*


Set up checkout
---------------
[Source](https://stripe.com/docs/checkout)

Add this script tag where you want the *Pay with Card* button - Change the demo data to real data, Publishable key, etc.

    <script
      src="https://checkout.stripe.com/v2/checkout.js" class="stripe-button"
      data-key="pk_test_0SVnVvF3igoJjPaQR63ivtkW"
      data-amount="2000"
      data-name="Demo Site"
      data-description="2 widgets ($20.00)"
      data-image="/128x128_logo.png">
    </script>

*The amount is always in cents*

Then handle the data

    jQuery(function($){
      var $stripeButton = $('.stripe-button');

      // Handle the token event
      $stripeButton.on('token', function(e, token){
        // Handle token.. e.g. Send token to payment endpoint
        $.ajax({
          url: '/pay',
          type: 'POST',
          data: { token: token.id }
        }).done(function(response){
          alert(response);
        });
      });
    });


Useful test card numbers:

 * `4242-4242-4242-4242`: Simulate a successful card transaction
 * `4000-0000-0000-0002`: Force a *card declined* response
 * `4242-4242-4242-4241`: Force an *invalid card number* response



Set up Server
-------------

### [Install Libraries](https://stripe.com/docs/libraries)

 Python

    sudo pip install --index-url https://code.stripe.com --upgrade stripe

    or
    sudo easy_install --index-url https://code.stripe.com --upgrade stripe


 Ruby

    sudo gem install --source https://code.stripe.com stripe

    or using bundler
    gem 'stripe', :git => 'https://github.com/stripe/stripe-ruby'

 PHP

    Download the source and load in your PHP application: [stripe-php-latest.tar.gz](https://code.stripe.com/stripe-php-latest.tar.gz)

### [Making a charge](https://stripe.com/docs/tutorials/charges)

Python

    # set your secret key: remember to change this to your live secret key in production
    # see your keys here https://manage.stripe.com/account
    stripe.api_key = "sk_test_hZ2CvfZPC6uXUf8kvnXgRMIs"

    # get the credit card details submitted by the form
    token = request.POST['stripeToken']

    # create a Customer
    customer = stripe.Customer.create(
        card=token,
        description="payinguser@example.com"
    )

    # charge the Customer instead of the card
    stripe.Charge.create(
        amount=1000, # in cents
        currency="usd",
        customer=customer.id
    )

    # save the customer ID in your database so you can use it later
    save_stripe_customer_id(user, customer.id)

    # later
    customer_id = get_stripe_customer_id(user)

    stripe.Charge.create(
        amount=1500, # $15.00 this time
        currency="usd",
        customer=customer_id
    )


Ruby

    # set your secret key: remember to change this to your live secret key in production
    # see your keys here https://manage.stripe.com/account
    Stripe.api_key = "sk_test_hZ2CvfZPC6uXUf8kvnXgRMIs"

    # get the credit card details submitted by the form
    token = params[:stripeToken]

    # create a Customer
    customer = Stripe::Customer.create(
      :card => token,
      :description => "payinguser@example.com"
    )

    # charge the Customer instead of the card
    Stripe::Charge.create(
        :amount => 1000, # in cents
        :currency => "usd",
        :customer => customer.id
    )

    # save the customer ID in your database so you can use it later
    save_stripe_customer_id(user, customer.id)

    # later
    customer_id = get_stripe_customer_id(user)

    Stripe::Charge.create(
        :amount => 1500, # $15.00 this time
        :currency => "usd",
        :customer => customer_id
    )

PHP

    // set your secret key: remember to change this to your live secret key in production
    // see your keys here https://manage.stripe.com/account
    Stripe::setApiKey("sk_test_hZ2CvfZPC6uXUf8kvnXgRMIs");

    // get the credit card details submitted by the form
    $token = $_POST['stripeToken'];

    // create a Customer
    $customer = Stripe_Customer::create(array(
      "card" => $token,
      "description" => "payinguser@example.com")
    );

    // charge the Customer instead of the card
    Stripe_Charge::create(array(
      "amount" => 1000, # amount in cents, again
      "currency" => "usd",
      "customer" => $customer->id)
    );

    // save the customer ID in your database so you can use it later
    saveStripeCustomerId($user, $customer->id);

    // later
    $customerId = getStripeCustomerId($user);

    Stripe_Charge::create(array(
        "amount" => 1500, # $15.00 this time
        "currency" => "usd",
        "customer" => $customerId)
    );

Resources
---------

 * [Full API documentation](https://stripe.com/docs/api)
 * [Setting up Heroku Hostname SSL with GoDaddy SSL Cert](https://gist.github.com/shripadk/552554)
