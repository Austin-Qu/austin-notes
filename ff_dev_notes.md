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
  * SCP syntax
    `scp deploy@ec2-54-252-204-134.ap-southeast-2.compute.amazonaws.com:/data/channel_manager/current/1.csv /Users/austinqu/Downloads`
    `scp 1.csv deploy@ec2-54-252-204-134.ap-southeast-2.compute.amazonaws.com:/tmp/ `
  * Pluck with Globalize
    `AttributeDefinition.includes(:translations).where(base_type: 12).pluck('attribute_definition_translations.code')`
  

### Get tenant name which has some certain channel

```
Tenant.where(version: 5).each do | tenant |
  Apartment::Tenant.switch(tenant) do
    puts tenant.name if Channel.where(type: 'AnatwineChannel').size > 0
  end
end
```

### Find all duplicated attribute_values

```
AttributeValue.limit(200).select(:product_id, :attribute_definition_id).group(:product_id, :attribute_definition_id).having("count(*) > 1").size
```

### OpenSSL issue for MAC OS

https://stackoverflow.com/questions/38670295/homebrew-refusing-to-link-openssl/38710248#38710248

None of these solutions worked for me on OS X El Capitan 10.11.6. Probably because OS X has a native version of openssl that it believes is superior, and as such, does not like tampering.

So, I took the high road and started fresh...

Manually install and symlink
cd /usr/local/src  
If you're getting "No such file or directory", make it:

cd /usr/local && mkdir src && cd src

Download openssl:

curl --remote-name https://www.openssl.org/source/openssl-1.0.2h.tar.gz
Extract and cd in:

tar -xzvf openssl-1.0.2h.tar.gz
cd openssl-1.0.2h
Compile and install:

./configure darwin64-x86_64-cc --prefix=/usr/local/openssl-1.0.2h shared
make depend
make
make install
Now symlink OS X's openssl to your new and updated openssl:

ln -s /usr/local/openssl-1.0.2h/bin/openssl /usr/local/bin/openssl
Close terminal, open a new session, and verify OS X is using your new openssl:

openssl version -a
(Using openssl-1.0.2o, working normally)

### Filter jobs in retry queue

```
re = Sidekiq::RetrySet.new
re.select do |j|
  j.retry if j.queue == 'iantesting5'
end
```

### Monkey-patch transmitter

1. Patch the code.
2. Set thread max to 0 for the tenant
3. Find the 'job', eg. an export scheduler
4. Run `job.scheduler.run(SecureRandom.uuid)` to create the sidekiq job
5. Find this job in 'enqueued', use the params to run the patched transmitter. eg.  `OroCommerce::Export::Transmitters::BatchTransmitter.new.perform(2, 17331)`


### Mock Prod Env from Local(PIM)

1. Replace content of config/environments/production.rb with config/environments/development.rb
2. Add 'production' section for config/database.yml and config/secrets.yml, using the content from 'development' section
3. Replace content of config/settings/production.rb with config/settings/developmment.rb(do this for both PIM and admin-central apps)
4. Comment out line "force_ssl" from app/controllers/application_controller.rb
