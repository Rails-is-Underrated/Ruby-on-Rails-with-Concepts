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


## Understanding JSON Web Tokens (JWT) 

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

```ruby
token = JWT.encode({message: 'Rails is fun!!'}, 'my_secret_key')
JWT.decode(token, 'my_secret_key')

```

In the first line, we encoded a payload with the secret key my_secret_key. So we get a token we can simply decode. 

The second line decodes the token, and we see that we find our payload well. 

Let's add a `json_web_token.rb` file in our `lib` directory.

```ruby
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

The method `JsonWebToken.encode` takes care of `encoding` the **payload** by adding an *expiration date* of 24 hours by default.

We also use the same encryption key as the one configured with Rails

The method `JsonWebToken.decode` decodes the JWT token and gets the payload. 

Then we use the `HashWithIndifferentAccess` class provided by Rails, which allows us to retrieve a value of a Hash with a Symbol or String.

#### Error Handling 

This class will handle our exceptions. 

Create a new file `lib/errors.rb`

```ruby
class Errors < Hash
  def add(key, value, _opts = {})
    self[key] ||= []
    self[key] << value
    self[key].uniq!
  end

  def add_multiple_errors(errors_hash)
    errors_hash.each do |key, values|
      values.each { |value| add key, value }
    end
  end

  def each
    each_key do |field|
      self[field].each { |message| yield field, message }
    end
  end
end
```

#### Authenticate User 

Let's create a class that will authenticate our users with valid email and password. 

`mkdir app/services`

`touch app/services/authenticate_user.rb`

```ruby
class AuthenticateUser < ApplicationService
  def initialize(email, password)
    @email = email
    @password = password
  end
  
  private 
  
  attr_accessor :email, :password

  def call
    # create token with user_id
    JsonWebToken.encode(user_id: user.id) if user
  end

  def user
    user = User.find_by(email: email)
    return user if user&.authenticate(password)
    errors.add :user_authentication, 'invalid credentials'
  end
end
```

We are inheriting from `ApplicationService` class, let's set it up

`touch app/services/application_service.rb`

```ruby
class ApplicationService
  
  # def self.call(*arg)
  class << self
    def call(*arg)
      new(*arg).constructor
    end
  end
  
  attr_reader :result
  def constructor
    # AuthenticateUser#call
    @result = call
    self
  end

  def success?
    !failure?
  end

  def failure?
    errors.any?
  end

  def errors
    @errors ||= Errors.new
  end

  def call
    fail NotImplementedError unless defined?(super)
  end
end
```

Now that we have our authenticate user logic in place let's authorize our request. 

#### Authorize API request

We are going to authorize all request that comes into our app.  

`touch app/services/authorize_api_request.rb`

```ruby
class AuthorizeApiRequest < ApplicationService
  attr_reader :headers

  def initialize(headers = {})
    @headers = headers
  end

  def call
    user
  end

  private

  def user
    @user ||= User.find(decoded_auth_token[:user_id]) if decoded_auth_token
    @user || errors.add(:token, 'invalid token') && nil
  end

  def decoded_auth_token
    # decodes a valid token 
    @decoded_auth_token ||= JsonWebToken.decode(http_auth_header)
  end

  def http_auth_header
    if headers['Authorization'].present?
      return headers['Authorization'].split(' ').last
    else
      errors.add(:token, 'missing token')
    end
    nil
  end
end
```

We are checking our headers, making sure it has a valid `token`, if true it returns a `user` object.

#### Set up token Controller

Let's create a file `touch app/controllers/sessions_controller.rb`

```ruby
class SessionsController < ApplicationController

  skip_before_action :authenticate_request

  def create
    auth = AuthenticateUser.call(params[:email], params[:password])
    if auth.success?
      render json: { auth_token: auth.result }
    else
      render json: { errors: auth.errors }, status: :unauthorized
    end
  end
end
```

Add a route, in `routes.rb` 

```ruby
post 'authenticate', to: 'sessions#create'
```

Set up the `application_controller`, 

```ruby
class ApplicationController < ActionController::API
  before_action :authenticate_request
  attr_reader :current_user

  private

  # authenticate all requests
  def authenticate_request
    auth = AuthorizeApiRequest.call(request.headers)
    @current_user = auth.result
    render json: { errors: auth.errors }, status: :unauthorized unless @current_user
  end
end
```

Checkout this [branch](https://github.com/Rails-is-Underrated/Duka/pull/3/files).

More on this please visit [Rails 6 API](https://github.com/Njunu-sk/api_on_rails/blob/master/rails6/en/chapter04-authentication.adoc) and [Todos API](https://www.digitalocean.com/community/tutorials/build-a-restful-json-api-with-rails-5-part-two).


***


See you in the next section 👉