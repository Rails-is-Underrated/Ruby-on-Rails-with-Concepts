# Action View 

In Rails, web requests are handled by **Action Controller** and **Action View**.
 
Typically, Action Controller is concerned with **communicating with the database and performing CRUD actions where necessary**. Action View is then responsible for **compiling the response**.

Controllers makes model data available to the view and this data can be **displayed** to the user.

In the, `index.html.erb`, paste the content below, 

```ruby
<h1>E commerce App</h1>


<% @products.each do |product| %>
  <p>Product Name: <%= product.name %></p>
  </p>Product Price: <%= product.amount_cents %></p>
<% end %>

```

You'll see your product listed below.

***

See you in the next section 👉
