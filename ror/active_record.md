# Active Record 

ActiveRecord is an interface that rails provides between the database and application.

## What is an ORM

In the past, web applications required the skills to code in your business logic language and your database language.

**ORM** is a technique that lets one manage a database in the business logic language that one is most comfortable with.

Rails uses ORM to map database tables to ruby objects.

Active Record as an ORM framework gives us several mechanisms, the most important are:
- represent models and their data, 
- represent associations between these models, 
- represent inheritance hierarchies through related models, 
- validate models before they get persisted to the database and perform database operations in an object-oriented fashion.

### Migrations

Migrations are scripts that tell rails how you want to set up or change a database, migrations are a convenient way to alter database schema over time.

#### Stand alone migrations.

Migrations are stored in the db/migrate directory, one for each migration class. 

Migrations will do more than just append timestamps, based on naming conventions and additional arguments it can also start fleshing out migrations.

If the migration name is of the form *AddColumnToTable* or *RemoveColumnFromTable* and is followed by a list of column names and types then a migration containing the appropriate `add_column` and `remove_column` statement will be created.

You can also add an *index* on the new column, that will generate an `add_index` statement.

Generators also accept column type as *references* (available as `belongs_to`), this generates `add_reference` call, this migration adds a *foreign_key* and an appropriate *index*.

There is also a generator that produces *JoinTables*.

The `change` Method, this is the primary way of writing migrations, it works for the majority of the cases where Active Record knows how to reverse the migrations automatically.

Migrations are usually reversible, this helps in case of errors . `rails db:rollback`, rolls back the last series of migrations.

In cases where one has a migration that can't be automatically undone, we have to specify the actions for doing and undoing the migrations.

Another thing to note is that migrations are not run when you create a new model, you have to run them manually. 

Rails `db:migrate` will run the change or up methods for all migrations that have not yet been run. 

The command above invokes `db:schema:dump` which updates `db/schema.rb` file to match the database structure.

Other useful commands;

`Rails db:reset`, drops the database and sets it up again, equivalent to `rails db:drop` `db:setup`.

Editing existing migrations because of legitimate cases is not recommended, one is advised to write new migrations that perform the changes he/she requires.

Editing migrations that have not yet been committed or editing migrations in development is relatively harmless.

### Validations

Validations ensure that data persisted to the database is correct data.

Data can be validated in several ways before it is saved into the database, including **client side- validation**, one can write javascript code in the browser which detects form fills and provides warnings in case of errors.

Native database constraints validates user data before being persisted to the **database** and **controller level validations**.

Native database constraints validation is preferred for it truly enforces constraints on the **database level**.

`ActiveModel::Errors` contains all errors, each error represented by an `ActiveModel::Error object`

- `errors[:attribute_name]` is used to check error message of a specific attribute it returns an array of strings with all error message for a given attribute

- displays an empty array if there are no errors related to the attribute.

### Callbacks

Callbacks let one run custom methods at certain moments of an object life cycle.

Rails ships with a number of callbacks, these methods can be used as a means to perform more logic **during**, **after**, **before** or **around** manipulations of active record objects.

Callbacks should be registered before being used. It is advised to declare callback methods as private to conform with the principle of object encapsulation.

**Relational callbacks** -> this is performed when related objects are changed.

**Conditional callbacks** -> used to render conditional logic, this can be achieved in many ways. First is using `:if` and `:unless` option which can take [a proc](https://www.section.io/engineering-education/understanding-closures-in-ruby/) or an array.

### Associations

An association is a connection between two Active Record objects, making common operations simpler and easier in the code.

Choosing between belongs_to and has_one: foreign key goes on the table of the class declaring the belongs_to association.

- belongs_to sets up a 1:1 connection with another model and has_one only contains one instance of another model.

- choosing between has_many :through and has_and_belongs_to_many: these associations declare many-to-many associations.

- has_and_belongs_to_many allows one to make associations directly and has_many :through makes an indirect association through a join model.

- polymorphic association -> assumes that a model belongs to more than one model on a single association.

## Product Model 

Let's add our `product` model, 

We a going to use a generator command, `rails g model product name amount_cents:integer`, 

This will generate several files, we will only focus on `app/models/product.rb` and `db/migrate/<timestamp>_create_products.rb`

Remember to run your migrations. Migrations are feature of Active Record that allow you to evolve your database schema over time.

`rails db:migrate`

Let's try out our newly created model 

`rails console`

```ruby
Product.create!(name: "E-book", amount_cents: 2000)

```

***

See you in the next section 👉
