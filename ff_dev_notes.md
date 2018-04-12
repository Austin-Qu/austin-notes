# FF dev notes

### To read

Gems: Apartment, Globalize, RequestStore

### How to tell V5 from other versions

V5: product_class, attribute_value
V1/2/3: product_level, attribute_set

### Env setup

[2.0 Local Dev Environment Setup](https://fusionfactory.atlassian.net/wiki/spaces/CC/pages/14745663/2.0+Local+Dev+Environment+Setup)

[Single Sign-On for Local Dev Environment](https://fusionfactory.atlassian.net/wiki/spaces/CC/pages/3571785/Single+Sign-On+for+Local+Dev+Environment)

### What to do if failed to create tenants via GUI

1. PIM: 
  * Comment out after_save/after_create callbacks in app/models/tenant.rb. In #initialize_db, run `Apartment::Tenant.create(self.name)` to generate schema for a tenant then #create this tenant instance
  * Run #initialize_s3, rest of #initialize_db and #initialize_sidekiq
  * Run #update_heartbeat
2. Create same tenant instance in AdminCentral, CM, OM under public schema, tid in PIM/CM/OM same as the tenant's id in AdminCentral
3. AdminCentral:
  * Run #populate in app/models/tenant.rb to generate admin user_group and other relations.
  
