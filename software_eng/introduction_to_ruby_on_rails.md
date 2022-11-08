# What is Rails?

Rails is a web application development framework written in the Ruby programming language, [Rails docs](https://guides.rubyonrails.org/getting_started.html#what-is-rails-questionmark).

## MVC 

Model View Controller is a design  pattern that divides related programming logic, making is easier to reason about. 

Rails follow this design pattern, [MVC](https://www.sitepoint.com/model-view-controller-mvc-architecture-rails/).

## Creating our Rails App

Create a directory and name it `Duka`, `mkdir duka`. 

Create a rails app with `rails new duka`

Navigate to the newly created `duka` folder. In the app folder, Rails maintains the files for the controllers, models, and views.

Create a route. In `config/routes.rb`. 

```

  Rails.application.routes.draw do
    root 'products#index'
  end

```

In the snippet above, we are instructing Rails to set the root route (/) to index action in ProductsController. So, the index action will be triggered when the user visits the root route.

We will run the following command to create the controller:

```
rails generate controller Products index --skip-routes
```

We added the --skip-routes, meaning we have defined the routes and to skip.

The generator will create files but the most important file is the controller file located in, app/controllers/products_controller.rb

```
  class ProductsController < ApplicationController
    def index
    end
  end
```

This generator also creates a file in app/views/landing/ named index.html.erb. By default, Rails renders a view that corresponds to the name of a controller action.

Replace the view content with:

```
<h1>E-commerce App</h1>
```

Let’s test our app by running:

```
rails s
```

We'll see our newly created application,

See you in the next section 👉,