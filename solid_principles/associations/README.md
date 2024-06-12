# Associations

An association is a connection between two Active Record models Using association we can easily find out how tables are linted to each other. It helps us to fetch and assign associated data easily (they make common operations simpler and easier in your code). There are six different types of associations:

### 1. belongs_to
### 2. has_one
### 3. has_many
### 4. has_many :through
### 5. has_one :through
### 6. has_and_belongs_to_many


## belongs_to association

Each instance of the declaring model "belongs to" one instance of the other model.
e.g:

```
class Comment < ActiveRecord::Base
  belongs_to :user
end
```

## belongs_to association

Each instance of the declaring model "belongs to" one instance of the other model.
e.g:

```
class Comment < ActiveRecord::Base
  belongs_to :user
end
```

```
class CreateBooks < ActiveRecord::Migration[7.1]
  def change
    create_table :authors do |t|
      t.string :name
    end

    create_table :books do |t|
      t.belongs_to :author
      t.datetime :published_at
    end
  end
end
```

When used alone, belongs_to produces a one-directional one-to-one connection. Therefore each book in the above example "knows" its author, but the authors don't know about their books. To setup a bi-directional association - use belongs_to in combination with a has_one or has_many on the other model, in this case the Author model.

belongs_to does not ensure reference consistency if optional is set to true, so depending on the use case, you might also need to add a database-level foreign key constraint on the reference column, like this:

```
create_table :books do |t|
  t.belongs_to :author, foreign_key: true
  # ...
end
```

## has_one association

One other model has a reference to this model

```
class Supplier < ApplicationRecord
  has_one :account
end
```

**The main difference from belongs_to is that the link column supplier_id is located in the other table:**

```
class CreateSuppliers < ActiveRecord::Migration[7.1]
  def change
    create_table :suppliers do |t|
      t.string :name
    end

    create_table :accounts do |t|
      t.belongs_to :supplier, index: { unique: true }, foreign_key: true
      t.string :account_number
    end
  end
end
```

This relation can be bi-directional when used in combination with belongs_to on the other model.

## 3. has_many association

A has_many association is similar to has_one, but indicates a one-to-many connection with another model. You'll often find this association on the "other side" of a belongs_to association. This association indicates that each instance of the model has zero or more instances of another model. For example, in an application containing authors and books, the author model could be declared like this:

```
class Author < ApplicationRecord
  has_many :books
end
```

```
class CreateAuthors < ActiveRecord::Migration[7.1]
  def change
    create_table :authors do |t|
      t.string :name
    end

    create_table :books do |t|
      t.belongs_to :author
      t.datetime :published_at 
    end
  end
end
```

Depending on the use case, it's usually a good idea to create a non-unique index and optionally a foreign key constraint on the author column for the books table:

```
create_table :books do |t|
  t.belongs_to :author, index: true, foreign_key: true
  # ...
end
```

## 4. has_many :through

Often used to set up a many-to-many connection with another model.
This association indicates that the declaring model can be matched with zero or more instances of another model by proceeding through a third model.

```
class Physician < ApplicationRecord
  has_many :appointments
  has_many :patients, through: :appointments
end

class Appointment < ApplicationRecord
  belongs_to :physician
  belongs_to :patient
end

class Patient < ApplicationRecord
  has_many :appointments
  has_many :physicians, through: :appointments
end
```

```
class CreateAppointments < ActiveRecord::Migration[7.1]
  def change
    create_table :physicians do |t|
      t.string :name
    end

    create_table :patients do |t|
      t.string :name
    end

    create_table :appointments do |t|
      t.belongs_to :physician
      t.belongs_to :patient
      t.datetime :appointment_date
    end
  end
end
```

## 5. has_one :through 
One-to-one connection with another model. This association indicates that the declaring model can be matched with one instance of another model by proceeding through a third model.

```
class Supplier < ApplicationRecord
  has_one :account
  has_one :account_history, through: :account
end

class Account < ApplicationRecord
  belongs_to :supplier
  has_one :account_history
end

class AccountHistory < ApplicationRecord
  belongs_to :account
end
```

```
class CreateAccountHistories < ActiveRecord::Migration[7.1]
  def change
    create_table :suppliers do |t|
      t.string :name
    end

    create_table :accounts do |t|
      t.belongs_to :supplier
      t.string :account_number
    end

    create_table :account_histories do |t|
      t.belongs_to :account
      t.integer :credit_rating
    end
  end
end
```

## 6. Has_and_belongs_to_many

A has_and_belongs_to_many association creates a direct many-to-many connection with another model, with no intervening model. This association indicates that each instance of the declaring model refers to zero or more instances of another model.

```
class CreateAssembliesAndParts < ActiveRecord::Migration[7.1]
  def change
    create_table :assemblies do |t|
      t.string :name
    end

    create_table :parts do |t|
      t.string :part_number
    end

    create_table :assemblies_parts, id: false do |t|
      t.belongs_to :assembly
      t.belongs_to :part
    end
  end
end

```

** How to pick has_many :through or has_and_belongs_to_many **

The simplest rule of thumb is that you should set up a has_many :through relationship if you need to work with the relationship model as an independent entity. If you don't need to do anything with the relationship model, it may be simpler to set up a has_and_belongs_to_many relationship (though you'll need to remember to create the joining table in the database).

You should use has_many :through if you need validations, callbacks, or extra attributes on the join model.

# Polymorphic association

With polymorphic associations, a model can belong to more than one other model, on a single association.

```
class Picture < ApplicationRecord
  belongs_to :imageable, polymorphic: true
end

class Employee < ApplicationRecord
  has_many :pictures, as: :imageable
end

class Product < ApplicationRecord
  has_many :pictures, as: :imageable
end
```

```
class CreatePictures < ActiveRecord::Migration[7.1]
  def change
    create_table :pictures do |t|
      t.string  :name
      t.bigint  :imageable_id
      t.string  :imageable_type
    end

    add_index :pictures, [:imageable_type, :imageable_id]
  end
end
```
This migration can be simplified by using the t.references form:
```
class CreatePictures < ActiveRecord::Migration[7.1]
  def change
    create_table :pictures do |t|
      t.string :name
      t.references :imageable, polymorphic: true
    end
  end
end
```

# Associations between Models with Composite Primary Keys

```
class Order < ApplicationRecord
  self.primary_key = [:shop_id, :id]
  has_many :books
end

class Book < ApplicationRecord
  belongs_to :order
end
```

# Self Joins

In designing a data model, you will sometimes find a model that should have a relation to itself. For example, you may want to store all employees in a single database model, but be able to trace relationships such as between manager and subordinates. This situation can be modeled with self-joining associations:

```
class Employee < ApplicationRecord
  has_many :subordinates, class_name: "Employee",
                          foreign_key: "manager_id"

  belongs_to :manager, class_name: "Employee", optional: true
end
```

# Tips

Here are a few things you should know to make efficient use of Active Record associations in your Rails applications:

**Controlling caching:** All of the association methods are built around caching, which keeps the result of the most recent query available for further operations. 

```
author.books.load # retrieves books from the database
author.books.size # uses the cached copy of books
author.books.empty? # uses the cached copy of books
author.books.reload.empty? # discards the cached copy of books and goes back to the database
```

**Avoiding name collisions:** You are not free to use just any name for your associations. Because creating an association adds a method with that name to the model, it is a bad idea to give an association a name that is already used for an instance method of ActiveRecord::Base. The association method would override the base method and break things. For instance, attributes or connection are bad names for associations.

**Updating the schema**
**Controlling association scope:** By default, associations look for objects only within the current module's scope. This can be important when you declare Active Record models within a module.
```
module MyApplication
  module Business
    class Supplier < ApplicationRecord
      has_one :account
    end

    class Account < ApplicationRecord
      belongs_to :supplier
    end
  end
end
```
This will work fine, because both the Supplier and the Account class are defined within the same scope. But the following will not work, because Supplier and Account are defined in different scopes:
```
module MyApplication
  module Business
    class Supplier < ApplicationRecord
      has_one :account
    end
  end

  module Billing
    class Account < ApplicationRecord
      belongs_to :supplier
    end
  end
end
```
To associate a model with a model in a different namespace, you must specify the complete class name in your association declaration:

```
module MyApplication
  module Business
    class Supplier < ApplicationRecord
      has_one :account,
        class_name: "MyApplication::Billing::Account"
    end
  end

  module Billing
    class Account < ApplicationRecord
      belongs_to :supplier,
        class_name: "MyApplication::Business::Supplier"
    end
  end
end
```

**Bi-directional associations:**It's normal for associations to work in two directions, requiring declaration on two different models:
```
class Author < ApplicationRecord
  has_many :books
end

class Book < ApplicationRecord
  belongs_to :author
end
```
Prevent needless queries for already-loaded data
Prevent inconsistent data
Autosave associations in more cases
Validate the presence and absence of associations in more cases

- touch
```
class Supplier < ApplicationRecord
  has_one :account, touch: true
end
```

- Conditional
```
has_one :account, -> { where "confirmed = 1" }
```