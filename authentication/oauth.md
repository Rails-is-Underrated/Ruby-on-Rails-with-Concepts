# Rails Authentication with OAuth 2.0 and OmniAuth 

*OAuth 2* is the industry-standard protocol for authorization, [OAuth 2.0](https://oauth.net/2/).

It enables a third-party application to obtain limited access to an HTTP service. 

We will implement OmniAuth with **Google**.

Overview of what happens when a user visits an app and tries to authenticate via a social google:

- User clicks *login* link 
- User is redirected to the social network's website. The app's data are sent along for identification. 
- User sees the app's details and which actions it would like to perform on his behalf. 
- User can cancel the authorization incase of trust issues. 
- If user trusts the app, the authorization is approved and the user is redirected back to the app(via callback URL). 
- Information about the user and a generated token(secret key) is sent along


## User Login


#### Configure Auth0 

Go to the [application_settings](https://manage.auth0.com/#/applications) and check out your application details. 

![](/images/app_details.png)

You need your apps **domain**, **Client_id**, **Client Secret**.

#### Configure Callback URLs 

This is the URL where Auth0 redirects the user after they have authenticated. 

Add the field in [application_settings](https://manage.auth0.com/#/applications).

#### Configure Logout URLs

Thi is a URL in the application that Auth0 can return to after the user has been logged out of the authorization server.

![](/images/url.png)


#### Install the Auth0 SDK

Add to your `Gemfile`: 

```
gem 'omniauth-auth0', '~> 3.0'
gem 'omniauth-rails_csrf_protection', '~> 1.0' # prevents forged authentication requests
```

Then `bundle install`. 

#### Create the configuration file 

Create an file in the root directory `touch auth0.yml`:

```
development:
  auth0_client_id: <%= ENV["AUTH0_CLIENT_ID"] %>
  auth0_client_secret: <%= ENV["AUTH0_CLIENT_SECRET"] %>
  auth0_domain: <%= ENV["AUTH0_DOMAIN"] %>

test:
  auth0_client_id: <%= ENV["AUTH0_CLIENT_ID"] %>
  auth0_client_secret: <%= ENV["AUTH0_CLIENT_SECRET"] %>
  auth0_domain: <%= ENV["AUTH0_DOMAIN"] %>

production:
  auth0_client_id: <%= ENV["AUTH0_CLIENT_ID"] %>
  auth0_client_secret: <%= ENV["AUTH0_CLIENT_SECRET"] %>
  auth0_domain: <%= ENV["AUTH0_DOMAIN"] %>

```

Add the `dotenv` gem and a new `.env` file in the root directory 

```
gem 'dotenv', '~> 2.8', '>= 2.8.1'
```

Run `bundle install`.

Add configuration in `touch config/initializers/auth0.rb`: 

```
Rails.application.config.auth0 = Rails.application.config_for(:auth0)

Rails.application.config.middleware.use OmniAuth::Builder do
  provider(
    :auth0,
    Rails.application.config.auth0['auth0_client_id'],
    Rails.application.config.auth0['auth0_client_secret'],
    Rails.application.config.auth0['auth0_domain'],
    callback_path: '/auth/auth0/callback',
    authorize_params: {
      scope: 'openid profile email'
    }
  )
end

```

#### Create an Auth0 controller 

This controller will handle the authentication callback: 

Create a file `touch app/controllers/sessions_controller.rb`

```
# ./app/controllers/sessions_controller.rb
class SessionsController < ApplicationController

  def create 
    session[:userinfo] = request.env['omniauth.auth']['extra']['raw_info'] 

    redirect_to '/'
  end

  def failure
    @error_msg = request.params['message']
  end
end

end
```

#### Add Routes

Add the followring routes in `routes.rb`

```
# ./config/routes.rb
Rails.application.routes.draw do
  # ..
  get '/auth/auth0/callback' => 'sessions#create'

  get '/auth/failure' => 'sessions#failure'

  resource :sessions, only: [:create, :destroy]
end
```


#### Add Logging in 

To redirect users to Auth0 for authentication, redirect users to `/auth/auth0` endpoint in your app.

```
<%= button_to 'Login', '/auth/auth0', method: :post, data: {turbo: "false"} %>
```

Click the Log In Button, 

![](/images/auth.png)


To be continued...

We have successfuly set up `OAuth` Authentication with Google. 

See you in the next section 👉