## Sending out Emails

Action Mailer allows you to send emails form our application using mailer classes and views.

We want to send emails to `customers` after placing an order.

Create a file in `app/mailers`, `order_mailer.rb`

```rb
class OrderMailer < ApplicationMailer
  default from: "Store <duka@gmail.com>"

  def received(order)
    @order = order 

    # attachments['receipt.pdf'] = SaleReceipt.new(@order).render 

    mail to: @order.email, subject: "Order Received"
  end
end
```

Note we are inheriting from `ApplicationMailer` class, mailers are similar to controllers in away.

Mailers have;
  - Actions and associated views in app/views
  - Instance variables accessible in views
  - The ability to utilise layouts and partials
  - The ability to access a params hash.

In the `order_mailer` class we have a **default** method that sets the default values for all emails sent from this mailer.

The **mail** method creates the actual email message.

Let's add our views, 

Create a file in `touch app/views/order_mailer/received.html.erb`, 

```
Dear <%= @order.name %>

<p>Thank you for your recent order.</p><br>

<hr>

<p>Please find your order details below.</p><br>

<em>We'll send a separate email when we ship your order.</em><br>


<hr>

Thank you for shopping with <%= @order.shop.name %>.
```

This is the HTML template.

We are able to call our mailers in our controller, 

```rb
OrderMailer.received(@order).deliver_now
```

Remember to add your environment credentials.

We are able to send out our order emails with attached receipts, check out [this branch](https://github.com/Rails-is-Underrated/Duka/pull/11/files#) for a full implementation.

***

See you in the next section 👉
