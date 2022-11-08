# Rails Authentication with OAuth 2.0 and OmniAuth 

*OAuth 2* is the industry-standard protocol for authorization, [OAuth 2.0](https://oauth.net/2/).

It enables a third-party application to obtain limited access to an HTTP service. 

We will implement OmniAuth with **Google** and **Devise**

Overview of what happens when a user visits an app and tries to authenticate via a social google:

- User clicks *login* link 
- User is redirected to the social network's website. The app's data are sent along for identification. 
- User sees the app's details and which actions it would like to perform on his behalf. 
- User can cancel the authorization incase of trust issues. 
- If user trusts the app, the authorization is approved and the user is redirected back to the app(via callback URL). 
- Information about the user and a generated token(secret key) is sent along


## Demo 

Add to your `Gemfile`: 

```
gem 'omniauth-google-oauth2'
```

Then `bundle install`. 

Add configuration in `config/initializers/devise.rb`: 

```
config.omniauth :google_oauth2, 'GOOGLE_CLIENT_ID', 'GOOGLE_CLIENT_SECRET', {}

```

To register *Google*, you need to provide the `client id` and `client secret`. 

### Google API Setup

- Go to [google Developer Console](https://console.developers.google.com).
- Select your project 
- Go to Credentials, then select the "OAuth consent screen" tab on top, and provide an 'EMAIL ADDRESS' and a 'PRODUCT NAME'
- Wait 10 minutes for changes to take effect.

#### Set-up a Callback URL

This is the URL where a user will be redirected to inside the app after successful authentication and approved authorization. 

In our `config/routes.rb`

```
devise_for :users, controllers: { omniauth_callbacks: 'users/omniauth_callbacks' }
```

#### Authentication Hash 

The `google` method inside the `Users::OmniauthCallbacksController` will parse the user data, save it into the database and perform sign in to the app. 

In our `users/omniauth_callbacks_controller.rb`, 

```
class Users::OmniauthCallbacksController < Devise::OmniauthCallbacksController
  def google
    @user = User.from_omniauth(request.env['omniauth.auth'])
    if @user.persisted?
      flash[:notice] = I18n.t 'devise.omniauth_callbacks.success', kind: 'Google'
      sign_in_and_redirect @user, event: :authentication
    else
      session['devise.google_data'] = request.env['omniauth.auth'].except('extra')
      redirect_to new_user_registration_url, alert: @user.errors.full_messages.join("\n")
    end
  end
end
```

#### Saving User Data 

Now that we have our `authentication_hash` up, we need to set up our `user` model , correctly. 

Add `omniauthable` to devise and a method to create a user if not found. 

```
class User < ActiveRecord::Base 
  devise :database_authenticatable, :registerable,
         :recoverable, :rememberable, :validatable
         :recoverable, :rememberable, :validatable, :omniauthable, omniauth_providers: [:github]

  def self.from_omniauth(access_token)
    data = access_token.info
    user = User.where(email: data['email']).first
    unless user
      user = User.create(
        email: data['email'],
        password: Devise.friendly_token[0,20]
      )
    end
    user
  end
end
```

We have successfuly set up `OAuth` Authentication with Google. 

See you in the next section 👉