# Rails Security 

Security revolves around three main concepts, 
 - Authentication
 - Authorization
 - Encryption

We are going to focus on `authentication` in this section.

In this guide we'll look into Rails vulnerabilities and how we ca go about it. 

Please clone [Rails Authentication from Scratch](https://github.com/Njunu-sk/rails-authentication-from-scratch) to follow along, 

## Session Fixation 

This attack is focused on fixing user's session ID known to the attacker and forcing user's browser into using this ID

This is solved with the `reset_session`, this issues a new session identifier and declares the old one invalid after a successful login.

This is demonstrated in the `Authentication` module: login and logout methods both have `reset_session`.


```ruby
# concerns/authentication.rb

  def login user
    reset_session
    active_session = user.active_sessions.create!(user_agent: request.user_agent, ip_address: request.ip)
    session[:current_active_session_id] = active_session.id

    active_session
  end

  def logout
    active_session = ActiveSession.find_by(id: session[:current_active_session_id])
    reset_session
    active_session.destroy! if active_session.present?
  end
```

## User Management 

User passwords must be stored only as cryptographic hash.

This is given in Rails with the `has_secure_password`, which supports secure password hashing, confirmation and recovery mechanism.

This is included in our `User` model along side `bcrypt gem`.

```ruby
# models/user.rb 

class user < ApplicationRecord 

has_secure_password

end
```

## Brute force Attack 

Accounts on the trial and error attack on the login page 

The best solution to this is to display a generic error message or require to enter a CAPTCHA

```ruby
# controller/sessions_controller.rb 

def create 
  ##

  flash.now[:alert] = "Incorrect email or password."
  render :new, status: :unprocessable_entity
end
```

## Account Hijacking 

### Passwords 
In scenarios where hacker is about to reset a user password after session fixation, provide attacker with a field to type current password.

This makes it more difficult for the attackers.

### Email 

Require user to enter a password when changing an email

```ruby
# controllers/users_controller.rb 

def update 
  ##
  
  if @user.authenticate(params[:user][:current_password])
  ##
end
```

This is just but a few security measures while authenticating a Rails app. 

***

See you in the next section 👉
