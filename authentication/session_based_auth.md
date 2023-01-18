# What is a session?

Session is just a place to **store data** during **one request** that you can **read** during **later requests**.

An **encrypted hash** in ruby, that stores data which can be sent forth and back in a request and can be **serialized**.

For a better understanding of what a session is, let's take a look and study **cookies**.

##### Cookies are set when a browser **responds back to  a request**. 

The browser will **store** those cookies until the cookies **expire**, every time one can make a request the browser will send the cookies back to the server.

##### What does this have to do with a session?

Cookies like session stores data from the client but they aren't always the right answer for session data.

Reasons why cookies are not equivalent to sessions:

- one can only store about 4kb of data in a cookie
- cookies are sent along with every request you make -> big cookies means bigger requests and responses which means slower browser.
- if you accidentally disclose secret_key_base, users can change the data you’ve put inside cookies.
- storing wrong data inside a cookie can be insecure.

### Alternative session stores

Sessions and cookies are similar but **sessions are more secure** because cookies only contain a **session ID** and in a Rails app looks for **data** in the **session store** using the same **ID**.

#### Cookie store, cache store, or database store ?

Storing sessions in cookies by far is the easiest way to go because it does not need any extra infrastructure or setup.

But if one wants to move beyond the cookie session store. One has two options, **store sessions** in the **database** or store them in the **cache**.

#### Storing sessions in the cache

Cache store is the second-easiest place to store session data, since it's already set up.

One does not need to worry about session stores growing out of control because older sessions will automatically get kicked out of the cache if it is too big.

This is **fast** because cache is kept in memory.

**But this is not perfect**:

- if you actually care about keeping old sessions around one would probably dont want them kicked off out of the cache.
- sessions and cache data will have minimal space causing cache misses and early expired sessions.
- if you ever need to reset cache, there is no way to do that without expiring everyone sessions.

#### Storing sessions in the database

If you want to keep your session data around until it legitimately expires, you probably want to keep it in some kind of database.

Database session storage also has drawbacks:

- with some database stores, sessions won't get cleaned up automatically.
- you have to know how the database will behave when its full of session data
- you have to be more careful about when you create session data, or you'll find your database with useless sessions.

# Session Based Authentication  

We store our session when we want our users to be loged in.

We are going to set up a session controller, 

Let's add the following routes: 

```ruby
# config/routes.rb 
get '/login', to 'sessions#new'
post '/login', to: 'sessions#create'
delete '/logout', to: 'sessions#destroy'
```

```ruby
class SessionsController < ApplicationController

  def new 
  end

  # create session.

  def create
    user = User.find_by(email: params[:session][:email].downcase)

    if user && user.authenticate(params[:session][:password])
      log_in user 
      redirect_back_or user 
    else
      flash.now[:danger] = 'Invalid email/password combination'
      render 'new'
    end  
  end

  # delete session

  def destroy 
    log_out
    redirect_to root_url 
  end

end
```

Let's add a `helper_method`, `session_helper.rb`

```ruby
module SessionsHelper 

 # accepts user and creates a session for the user.
  def log_in(user)
    session[:user_id] = user.id 
  end

 # returns a current user if present in the session. 
  def current_user 
    if session[:user_id]
      @current_user ||= User.find_by(id: session[:user_id])
    end
  end

  def logged_in?
    !current_user.nil? 
  end

 # logs out user by deleting session and setting current_user to nil 
  def log_out
    session.delete(:user_id)
    @current_user = nil
  end

  def current_user?(user)
    user == current_user 
  end

 # redirect non logged in user to the first visited url after loggin in. 

  def store_location 
    session[:forwarding_url] = request.original_url if request.get?
  end
end
```

This `session helper` will be imported in the application controller. Making the methods available to all controllers. 

```ruby
class ApplicationController <  ActionController::Base

  include SessionsHelper

  private 

  def logged_in_user 
    unless logged_in? 
      store_location 
      flash[:danger] = "Please log in."
      redirect_to login_url 
    end
  end
end
```

We need to add a `before_action` filter in our controller to restrict our users, 

```ruby
# products_controller.rb 

class ProductsController < ApplicationController 

  before_action :logged_in_user
```

Check out this [branch](https://github.com/Rails-is-Underrated/Duka/pull/2/files).

This are the core components in a `session based authentication`, you can check out [How to set up authentication form scratch](https://www.section.io/engineering-education/how-to-setup-user-authentication-from-scratch-with-rails-6/) or [Session Based Authentication](https://hackernoon.com/building-a-simple-session-based-authentication-using-ruby-on-rails-9tah3y4j) blogs.

***

See you in the next section 👉



