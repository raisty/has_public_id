# Public Id

## Description

Simplifies the generation and use of unique, random identifiers for ActiveRecord models.

We generate a string of random (using SecureRandom) alphanumeric characters (A-Z, 0-9) along with an optional prefix that defaults to the first 3 characters of the model name. The random string has a default length of 12.

On assignment to the model, if duplicate ID is detected in
the database, a new random identifier is generated.

## Installation
Add to Gemfile / etc

    gem 'has_public_id', git: 'https://github.com/raisty/has_public_id', branch: 'master'

## Usage

Add an additional identifier column. I called it "ident", but call it whatever you want.
```sh
# Identifier column MUST be a string
rails generate migration add_ident_to_users ident:string
```

Tell your activerecord object that ident is your new public identifier.
```ruby
class User < ActiveRecord::Base
  has_public_id :ident, prefix: 'user'
  # Automatically defines to_param as :ident
end
User.new.ident
# => "user-ECNrdIzvCBh8"

```

Now change your controllers to lookup the public ID instead of your database ID
```ruby
class UserController < ApplicationController
  def show
    User.find_by_public_id(params[:id])
    # Or User.find_by_ident(params[:id])
    # Nothing fancy here
  end
end
```

There's a few other convenience methods that you may find useful.

 * Initialize ID's for existing records, useful in a migration.

    ```ruby
      def change
        add_column :users, :ident, :string
        User.initialize_public_ids!
      end
    ```

  * Get a new random ID for your own nefarious purposes:

  ``` User.new_public_id ```

### Configuration

By default, ID's have 2 components.
A 3 character lowercase prefix of their originating class name and a suffix of a 14 character random, unique, base64 url safe string.
The suffix and prefix are joined by a dash.

You can skip the prefix alltogether:
```ruby
  has_public_id column_name, length: 10, prefix: false
```
or set it directly:
```
  has_public_id other_column_name, length: 15, prefix: 'user_'
```
The "length" option refers to the length argument passed to SecureRandom. The actual length
of the random base64 string will be about 4/3's this length. The defaults to 10, for a
14 character base64 string.

# Contribute

  * Fork this project
  * Add tests
  * Submit a pull request

This project rocks and uses MIT-LICENSE.
