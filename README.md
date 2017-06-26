# austin-repo
### Austin's dev notes

## Environment Setup

1.intsall rails on OSX when nokogiri compile error occurred (libxml-2.0 is not found)

`xcode-select --install`

2.Start/stop Postgres

```
pg_ctl -D /usr/local/var/postgres -l /usr/local/var/postgres/server.log start
pg_ctl -D /usr/local/var/postgres stop -s -m fast
```

## Rails

1.rails 5 changed:
in `new_framework_defaults.rb`
`Rails.application.config.active_record.belongs_to_required_by_default = true` has been preset. ref: http://blog.bigbinary.com/2016/02/15/rails-5-makes-belong-to-association-required-by-default.html

2.Use SASS @import instead of Sprockets directives
SASS @import creates a global namespace, while the Sprockets directives do not.
ref:https://content.pivotal.io/blog/structure-your-sass-files-with-import

3.Use Rspec to generate test documentation
`rspec --format documentation`

4.ENV variables not working in rails console, try to restart spring
`stop spring`

5.Float vs Decimal

Use float when you don't care about precision too much. For example, some scientific simulations and calculations only need up to 3 or 4 significant digits. This is useful in trading off accuracy for speed. Since they don't need precision as much as speed, they would use float.

Use decimal if you are dealing with numbers that need to be precise and sum up to correct number (like compounding interests and money-related things). Remember: if you need precision, then you should always use decimal.
FYI:https://stackoverflow.com/questions/8514167/float-vs-decimal-in-activerecord
