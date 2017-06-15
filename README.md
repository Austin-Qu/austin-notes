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
