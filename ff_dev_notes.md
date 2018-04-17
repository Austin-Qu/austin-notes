# FF dev notes

### To read

Gems: Apartment, Globalize, RequestStore

### How to tell V5 from other versions

V5: product_class, attribute_value

V1/2/3: product_level, attribute_set

### Env setup

[2.0 Local Dev Environment Setup](https://fusionfactory.atlassian.net/wiki/spaces/CC/pages/14745663/2.0+Local+Dev+Environment+Setup)

[Single Sign-On for Local Dev Environment](https://fusionfactory.atlassian.net/wiki/spaces/CC/pages/3571785/Single+Sign-On+for+Local+Dev+Environment)

### What to do if failed to create tenants via GUI (due to PG transaction error)

1. PIM: 
  * Comment out after_save/after_create callbacks in app/models/tenant.rb. In #initialize_db, run `Apartment::Tenant.create(self.name)` to generate schema for a tenant then #create this tenant instance
  `Tenant.create(tid: 2, name: 'austinv3', version: 3, status: 0)`
  * Run #initialize_s3, rest of #initialize_db and #initialize_sidekiq. Temporarily delete 20160602010710_insert_gtin_system_attribute.rb run tenant migrate, seed then put it back
  `git co db/migrate/20160602010710_insert_gtin_system_attribute.rb`
  * Run #update_heartbeat
2. Generate schema for the same tenant in CM/OM if not created by GUI 1st time. Create same tenant instance in AdminCentral, CM, OM under public schema, tid in PIM/CM/OM same as the tenant's id in AdminCentral
3. AdminCentral:
  * Run #initialize_tenant in app/models/tenant.rb to generate admin user_group and other relations.
  
### Misc

  * ruby 2.4 not compatible with rest-client for now (ruby 2.2 recommended, 2.3 seems all good)
  * restart POW if nothing hits local server
  * Heartbeat to update products in Export items
  * Stage after new channel built, **heartbeat** if channel fields order changed, **stage** again if channel fields content changed
  * When to choose multiple source classes in field: when multiple product class hierarchies exist, normally only choose 1  class on SKU class type.
