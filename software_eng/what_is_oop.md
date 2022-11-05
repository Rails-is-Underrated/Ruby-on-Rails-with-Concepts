# What is object Oriented Programming?

Object Oriented Programming (OOP) is a programming paradigm that relies on the concept of classes and objects.

It structures a software program into a simple, reausable pieces of code blueprints(classes), which are used to create individual instances of objects.

For example a **line_item** is an object which has certain **properties** such as **quantity**, **product_id**, etc. It also has certain **methods** such as **calculate line item's total cost** etc.


![](/images/line_item.png)


We can create an instance of a **line_item** type object, **line_item_one** to represent a specific **line_item**. 

```
line_item_one = LineItem.new
# properties
line_item_one.quantity()
line_item_one.product_id 

# methods
line_item_one.total_cost()
```

We could then set the value of the properties defined in the class to describe **line_item**, without affecting other objects or the class template.

We can then reuse this class to represent any number of line_items.

## Ruby is an Object-Oriented Language 

Everything you manupulate in Ruby is an object and the results of those manupulations are themselves objects. 

When you write object-oriented code, you're normally looking to model concepts from the real world.

You create objects by calling a constructor, a special method associated with a class. 

The standard contructor is called new(), you invoke methods by sending a message to an object.


See you in the next section 👉