# Sinatra API project

Sinatra is a DSL for quickly creating web applications in Ruby with minimal effort.

Check out their [docs](https://sinatrarb.com/)

We are going to create a simple app that will perform `CRUD` operations on our product resource.

## Product Application 

### Prerequisites

To follow along, it is helpful to have the following 

- Basic Ruby knowledge.

- Understand how the [web works](https://simonnjunu16.medium.com/understanding-the-osi-model-for-developers-f033a6adc9e).

- Postgresql and ruby Installed. 

- How to use postman.

Above all, let's have fun!

### Let the games begin. 

I assume that you have your editor configured, create a directory and name it `product_api`

`
mkdir product_api  
`

#### Set up Sinatra Application 

In our directory, `bundle init` in your terminal, this will create a Gemfile. 

**Add this [gems](https://rubygems.org/)** to your Gemfile.

```

gem "sinatra", "~> 3.0"
gem "http", "~> 5.1"
gem 'puma'
gem "pg"    # for Postgres
gem "rake"  # so we can run Rake tasks
gem "sinatra-activerecord"    # for Active Record models
gem 'rack-contrib', '~> 2.3'

group :development, :test do
  gem "rerun"
end
```

run  `bundle install`

**Create a config.ru file**

`touch config.ru`

Copy and paste the above

```
require 'bundler'
require 'rack'
require 'rack/contrib'

Bundler.require 

use Rack::JSONBodyParser
require_relative './product.rb'

run ProductAPI
```

We have alot going on here!, basically we are requiring packages from our `Gemfile` ☝️.

**Rack** is a layer between the framework and the application server, [good read](https://simonnjunu16.medium.com/rack-2d66f82eba9f).

**Let's set up rake**

Create a file in the `root` directory, 

`touch Rakefile`

And copy the above 

```
require 'sinatra/activerecord'
require 'sinatra/activerecord/rake'
# require './product'

```

**Set up database**

We are going to use postgres, in our `root` directory create a new directory `config` and add a file `database.yml`.

`
mkdir config
`

`
cd config
`

`
touch database.yml 
`

Copy and paste the code above, 

```
development:
  adapter: postgresql
  encoding: unicode
  database: database_name
  pool: 2
  username: add_psql_user # TO BE DONE
  password: add_psql_password # TO BE DONE
production:
  adapter: postgresql
  encoding: unicode
  pool: 5
  host: <%= ENV['DATABASE_HOST'] %>
  database: <%= ENV['DATABASE_NAME'] %>
  username: <%= ENV['DATABASE_USER'] %>
  password: <%= ENV['DATABASE_PASSWORD'] %>
```

Now that we have our database configured let's add our models. 

**Note**: Change your postgres configurations.

**Set up model**

In our `root` directory create a new folder `mkdir model`. 

Our `model` folder is going to have a simple ruby class, create a file `touch product.rb`.

Copy this ruby script: 

```
class Product < ActiveRecord::Base 

end
```

Dont worry about the `ActiveRecord::Base` class we'll revisit it. 

**Add our migrations**

Create a folder in `root` directory `mkdir db`.

Run the above command, 

`rake db:create_migration NAME=create_products`

In the newly generated file modify it to,

```
class CreateProducts < ActiveRecord::Migration[7.0]
  def change
    create_table :products do |t|
      t.string :name, null: false, default: ""
      t.integer :amount_cents, null: false, default: 0

      t.timestamps
    end
  end
end
```

Then run, 

`rake db:migrate`

**Set up a controller**

Create a new file in `root` directory `touch product.rb`, and copy the above content. 

Please read the comments above: 

```
require 'sinatra/base' 
require 'json'     
require 'sinatra/activerecord'

set :database_file, 'config/database.yml'
$: << File.dirname(__FILE__) + "/model" # Assuming app.rb is at the same level as lib

require 'product'

class ProductAPI < Sinatra::Base 

  get '/products' do 
    # return product_array.to_json
    Product.select('id', 'name', 'amount_cents').all.to_json
  end

  get '/products/:id' do
    # return a particular product as json based on the id param from the url
    # params always come to a string so we convert to an integer
    # id = params["id"].to_i
    # return product_array[id].to_json
    product =  Product.find_by_id(params[:id])

    if product
      product.to_json 
    else
      halt 404
    end
  end

  post '/products' do
    # create the new product
    # new_product = {name: body["name"], amount_cents: body["amount_cents"]}
    # push the new product into the array
    # product_array.push(new_product)
    # return the new product
    # return product_array.to_json
    # params = {name: body["name"], amount_cents: body["amount_cents"]}
    product = Product.create(params)

    if product
      product.to_json
    else
      halt 500
    end
  end

  put '/products/:id' do
    # get the id from params
    # id = params["id"].to_i
    # update the item
    # product_array[id][:name] = body["name"]
    # product_array[id][:amount_cents] = body["amount_cents"]
    # return the updated product
    # return product_array[id].to_json
    product = Product.find_by_id(params[:id])

    if product
      product.update(params)
    else
      halt 404
    end
  end

  delete '/products/:id' do
    # get the id from params
    # id = params["id"].to_i
    # delete the item
    # product = product_array.delete_at(id)
    # return the deleted item as json
    # return product.to_json
    product = Product.find_by_id(params[:id])

    if product
      product.destroy
    else
      halt 404
    end
  end

end
```

Check out this [Blog](https://dev.to/alexmercedcoder/creating-a-full-crud-api-with-ruby-sinatra-15b4).

**NOTE** Uncomment the `require './product'` in `Rakefile`.

We have created a simple API application 😎, let's fire up postman and our application. 

Run `bundle exec rerun rackup` to start the server.

Yeey!! 💪. 

## End points 

| verb   | Endpoint                         | Action | Functionality             |
|--------|----------------------------------|--------|---------------------------|
| GET    | http://127.0.0.1:9292/products   | index  | All products              |
| GET    | http://127.0.0.1:9292/products/1 | show   | Display product with id 1 |
| POST   | http://127.0.0.1:9292/products   | create | Create a new product      |
| PUT    | http://127.0.0.1:9292/products/1 | update | Update product with id 1  |
| DELETE | http://127.0.0.1:9292/products/1 | delete | Delete product with id 1  |

Make sure you include the body fields in `POST` and `PUT` request. 


Nice work!, 

See you in the next section 👉


