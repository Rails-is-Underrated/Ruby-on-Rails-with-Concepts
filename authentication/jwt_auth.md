# JSON

When exchanging data between a browser and a server, data can only be text format.

JSON is text that we can convert into javascript objects and send it to the server. 

We can also convert any JSON received from the server into javascript objects, this enables us to work with the data as Javascript objects with no complicated parsing and translations.

JSON makes it possible to store javascript objects as text. 

JSON has two structures;

- a collection of name/value pairs => hash
- an ordered list of values => array

JSON is lightweight data-interchange format

JSON is **self-describing** and easy to understand

JSON is language independent.

`JSON.parse()` -> this is a javascript built in function to convert a string written in JSON format into native JavaScript objects.


## Understanding JSOn web Tokens (JWT) 

A JSON web Token(JWT) is a JSON object defined in RFC 7519 as a safe way to represent a set of information between two parties, [more](https://morioh.com/p/63009714b79a). 

The token is composed of a **header**, a **payload**, and a **signature**.

JWt is used for **authorization** not **authentication**.

To illustrate how it works we use 3 simple entities, the **user**, **application server** and the **authentication server**.

- User signs into the authentication server using a login system 
- The authentication server then creates the JWT and sends it to the user. 
- When user makes an API call to the application, user passes a JWT along with the API call, in the setup the application server would be configured to verify that the incoming JWT are created by the authentication server.
- When user makes API calls with the attached JWT, the application can use the JWT to verify that the API call is coming from an authenticated user.

### API should be stateless 

An API does not handle sessions, an API should be stateless.

#### Setting up the authentication token 

Install `jwt`, 

```
bundle add jwt
```

The library has two methods, `JWT.encode` and `JWT.decode`. 

Open the `rails console`

```
token = JWT.encode({message: 'Hello World'}, 'my_secret_key')
JWT.decode(token, 'my_secret_key')

```

In the first line, we encoded a payload with the secret key my_secret_key. So we get a token we can simply decode. 

The second line decodes the token, and we see that we find our payload well. 

Let's add a `json_web_token.rb` file in our `lib` directory.

```
class JsonWebToken 
  # secret to encode and decode token
  HMAC_SECRET = Rails.application.secrets.secret_key_base

  def self.encode(payload, exp = 24.hours.from_now)
    # set expiry to 24 hours from creation time
    payload[:exp] = exp.to_i
    # sign token with application secret
    JWT.encode(payload, HMAC_SECRET)
  end

  def self.decode(token)
    # get payload; first index in decoded Array
    decoded = JWT.decode(token, HMAC_SECRET)[0]
    HashWithIndifferentAccess.new decoded
  end
end
```

The method JsonWebToken.encode takes care of encoding the payload by adding an expiration date of 24 hours by default.

We also use the same encryption key as the one configured with Rails

The method JsonWebToken.decode decodes the JWT token and gets the payload. 

Then we use the HashWithIndifferentAccess class provided by Rails, which allows us to retrieve a value of a Hash with a Symbol or String.

#### Token's Controller 

We have, therefore, set up the system for generating a JWT token. 

It’s now time to create a route that will generate this token. 

The actions we will implement will be managed as RESTful services: the connection will be managed by a POST request to the create action.

We will start by creating the controller of and method create in the namespace /API/v1. With Rails, one order is sufficient:

```
rails generate controller api::v1::tokens create

```

Configure routes, 

```
Rails.application.routes.draw do
  namespace :api, defaults: { format: :json } do
    namespace :v1 do
      # ...
      resources :tokens, only: [:create]
    end
  end
end
```

##### Create JWT token logic. 

```
# app/controllers/api/v1/tokens_controller.rb

class Api::V1::TokensController < ApplicationController
  def create
    @user = User.find_by_email(user_params[:email])
    if @user&.authenticate(user_params[:password])
      render json: {
        token: JsonWebToken.encode(user_id: @user.id),
        email: @user.email
      }
    else
      head :unauthorized
    end
  end

  private

  # Only allow a trusted parameter "white list" through.
  def user_params
    params.require(:user).permit(:email, :password)
  end
end
```

More on this please visit [Guide](https://github.com/Njunu-sk/api_on_rails/blob/master/rails6/en/chapter04-authentication.adoc) and [this](https://www.digitalocean.com/community/tutorials/build-a-restful-json-api-with-rails-5-part-two).


See you in the next section 👉