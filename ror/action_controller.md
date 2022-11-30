# Action Controller 

Routers determine which controller to use for the request. Controllers will then receive the request and save or fetch data from our models.

A **controller** can thus be thought of as a middleman between **models** and **views**. 

It makes the model data available to the view, so it can display that data to the user, and it saves or updates user data to the model.

Let’s add an `index` action in our `products_controller`, to fetch the newly created `product`.

```ruby
def index 
  @products = Product.all
end
```

In the code above, if a **seller** goes to `/` in your application, Rails will create an instance of `ProductsController` and call its `index` method.

Rails will render the `index.html.erb` view .

By **fetching** all products, the `index` method can make a `@products` instance variable accessible in the **view**.

***

See you in the next section 👉

