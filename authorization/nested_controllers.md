# Nested Controllers 

You would probably want to set up an admin section in your application. 

Let's set up nested controllers. 

Vendors should create a *shop* add *products* and it's *category*. 

In our `app/routes.rb` add the resources above 

```ruby

#### 

namespace :admin do 
  resources :shops 
  resources :products 
  resources :categories 

  ##
end

####
```

We are organizing groups of controllers under a namespace, `Admin::` namespace, these controllers are placed under `app/controllers/admin` directory. 

Create a directory `admin` in our controllers and create a new file `base_controller.rb`. 

`mkdir admin`

`touch admin/base_controller.rb`

Let's configure our admin section 

```ruby
module Admin 
  class BaseController < ApplicationController

  end
end
```

Then our `shops_controller`

```ruby 
class Admin::ShopsController < Admin::BaseController
  layout 'admin/base'

end
```

We are inheriting from  `Admin::BaseController` and using a different layout for our views, check the full implementation on this blog [Admin Section](https://simonnjunu16.medium.com/setting-up-authentication-and-admin-section-in-rails-2c58b1186cbc).

***

See you in the next section 👉
