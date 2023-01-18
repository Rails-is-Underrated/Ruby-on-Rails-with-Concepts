# Roles from Scratch

We are able to visit an `admin/shops` route and redirected to a different layout, this must be resticted to only `admin` users. 

We will need a `role` model, 

`rails g model role name reference access:integer`

This model should be associated with our user through a `user_role` model, 

`rails g model user_role user:belongs_to role:belongs_to`

Before migrating our newly created migration. let's modify the `role` migration and set up the relationships

We are limiting our `access` field to a single digit and set it's default to zero.

```ruby
class CreateRoles < ActiveRecord::Migration[7.0]
  def change
    create_table :roles do |t|
      t.string :name
      t.string :reference
      t.integer :access, limt: 1, default: 0

      t.timestamps
    end
  end
end

```

- Role model 

```ruby
class Role < ApplicationRecord
  has_many :user_roles, dependent: :destroy 

  enum access: {
    viewable: 0, 
    createable: 1, 
    editable: 2
  }
end

```

Our [enum](https://edgeguides.rubyonrails.org/active_record_querying.html#enums) will create instance methods that can be used to determine if an object has a particular value for the enum.

- User Role Model 

```ruby 
class UserRole < ApplicationRecord
  belongs_to :user
  belongs_to :role
end

```

- User Model 

```ruby 
class User < ApplicationRecord
  has_many :shops, dependent: :destroy
  has_many :user_roles, dependent: :destroy
  has_many :roles, through: :user_roles 

  ##

  def can_edit?(resource)
    resource_class = resource.class.to_s == "Class" ? resource.name : resource.class.to_s 
    role = roles.where(reference: resource_class)
    return false unless role 

    role.map(&:editable?).any? || role.map(&:createable?).any? 
  end

  def can_create?(resource)
    resource_class = resource.class.to_s == "Class" ? resource.name : resource.class.to_s 
    role = roles.where(reference: resource_class)
    return false unless role 

    role.map(&:createable?).any? 
  end

  def add_admin 
      # set up Role in seed file
    admin_pdct_role = Role.find_by(name: "Admin Product")
    editor_pdct_role = Role.find_by(name: "Editor Product")
  

    if createable.empty? && editable.empty?
      user_roles.create!(role: admin_pdct_role)
      user_roles.create!(role: editor_pdct_role)
    end
  end
end

```

The `can_edit?` and `can_create?` methods restrict a logged user from accessing a resource.

Let's add some helpers so that our methods can be accessed in our views.

```ruby 
module ApplicationHelper

  def can_edit?(resource)
    return false unless user_signed_in?

    current_user.can_edit?(resource)
  end

  def can_create?(resource)
    return false unless user_signed_in?

    current_user.can_create?(resource) 
  end
  
  #
end

```

More on [roles](https://www.youtube.com/watch?v=htoAZlv-SBM).
***

See you in the next section 👉
