# Austin's dev notes

## Environment Setup

1. Intsall rails on OSX when nokogiri compile error occurred (libxml-2.0 is not found)

`xcode-select --install`

2. Start/stop Postgres

```
pg_ctl -D /usr/local/var/postgres -l /usr/local/var/postgres/server.log start
pg_ctl -D /usr/local/var/postgres stop -s -m fast
```

Use brew service to autorun Postgres, `createuser xxxxxx` to create user.

## Rails

1. Rails 5 changed:
in `new_framework_defaults.rb`
`Rails.application.config.active_record.belongs_to_required_by_default = true` has been preset. ref: http://blog.bigbinary.com/2016/02/15/rails-5-makes-belong-to-association-required-by-default.html
`belongs_to :user, optional: true` to set that not required

2. Use SASS @import instead of Sprockets directives
SASS @import creates a global namespace, while the Sprockets directives do not.
ref:https://content.pivotal.io/blog/structure-your-sass-files-with-import

3. Use Rspec to generate test documentation
`rspec --format documentation`

4. ENV variables not working in rails console, try to restart spring
`stop spring`

5. Float vs Decimal

Use float when you don't care about precision too much. For example, some scientific simulations and calculations only need up to 3 or 4 significant digits. This is useful in trading off accuracy for speed. Since they don't need precision as much as speed, they would use float.

Use decimal if you are dealing with numbers that need to be precise and sum up to correct number (like compounding interests and money-related things). Remember: if you need precision, then you should always use decimal.
FYI:https://stackoverflow.com/questions/8514167/float-vs-decimal-in-activerecord

## Heroku

1. Use `heroku pg:reset DATABASE_URL` to recreate postgresql database


## Git

To check changes from last pull

* See all of the changes: git diff master@{1} master
* See the changes to a given file: git diff master@{1} master <file>
* See all the changes within a given directory: git diff master@{1} master <dir>
* See the summary of changes again: git diff --stat master@{1} master

HEAD is the name for the currently checked out branch.

