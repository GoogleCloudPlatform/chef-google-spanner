# Google Spanner Chef Cookbook

This cookbook provides the built-in types and services for Chef to manage
Google Cloud Spanner resources, as native Chef types.

## Requirements

### Platforms

#### Supported Operating Systems

This cookbook was tested on the following operating systems:

* RedHat 6, 7
* CentOS 6, 7
* Debian 7, 8
* Ubuntu 12.04, 14.04, 16.04, 16.10
* SLES 11-sp4, 12-sp2
* openSUSE 13
* Windows Server 2008 R2, 2012 R2, 2012 R2 Core, 2016 R2, 2016 R2 Core

## Example

```ruby
gspanner_instance 'my-spanner' do
  action :create
  display_name 'My Spanner Instance'
  node_count 2
  labels [
    {
      'cost-center' => 'ti-1700004'
    }
  ]
  config 'regional-us-central1'
  project ENV['PROJECT']
  credential 'mycred'
end

gspanner_database 'webstore' do
  action :create
  instance 'my-spanner'
  extra_statements [
    'CREATE TABLE customers (
       customer_id INT64 NOT NULL,
       last_name STRING(MAX)
     ) PRIMARY KEY (customer_id)'
  ]
  project ENV['PROJECT']
  credential 'mycred'
end
```

## Credentials

All Google Cloud Platform cookbooks use an unified authentication mechanism,
provided by the `google-gauth` cookbook. Don't worry, it is automatically
installed when you install this module.

### Example

```ruby
gauth_credential 'mycred' do
  action :serviceaccount
  path ENV['CRED_PATH'] # e.g. '/path/to/my_account.json'
  scopes [
    'https://www.googleapis.com/auth/spanner.admin'
  ]
end

```

For complete details of the authentication cookbook, visit the
[google-gauth][] cookbook documentation.

## Resources

* [`gspanner_instance`](#gspanner_instance) -
    An isolated set of Cloud Spanner resources on which databases can be
    hosted.
* [`gspanner_database`](#gspanner_database) -
    A Cloud Spanner Database which is hosted on a Spanner instance.


### gspanner_instance
An isolated set of Cloud Spanner resources on which databases can be
hosted.


#### Example

```ruby
gspanner_instance 'my-spanner' do
  action :create
  display_name 'My Spanner Instance'
  node_count 2
  labels({
    'cost-center' => 'ti-1700004'
  })
  config 'regional-us-central1'
  project ENV['PROJECT']
  credential 'mycred'
end

```

#### Reference

```ruby
gspanner_instance 'id-for-resource' do
  config       reference to gspanner_instance_config
  display_name string
  labels       namevalues
  name         string
  node_count   integer
  project      string
  credential   reference to gauth_credential
end
```

#### Actions

* `create` -
  Converges the `gspanner_instance` resource into the final
  state described within the block. If the resource does not exist, Chef will
  attempt to create it.
* `delete` -
  Ensures the `gspanner_instance` resource is not present.
  If the resource already exists Chef will attempt to delete it.

#### Properties

* `name` -
  A unique identifier for the instance, which cannot be changed after
  the instance is created. Values are of the form
  projects/<project>/instances/[a-z][-a-z0-9]*[a-z0-9]. The final
  segment of the name must be between 6 and 30 characters in length.

* `config` -
  A reference to the instance configuration.

* `display_name` -
  Required. The descriptive name for this instance as it appears in UIs. Must be
  unique per project and between 4 and 30 characters in length.

* `node_count` -
  The number of nodes allocated to this instance.

* `labels` -
  Cloud Labels are a flexible and lightweight mechanism for organizing
  cloud resources into groups that reflect a customer's organizational
  needs and deployment strategies. Cloud Labels can be used to filter
  collections of resources. They can be used to control how resource
  metrics are aggregated. And they can be used as arguments to policy
  management rules (e.g. route, firewall, load balancing, etc.).
  Label keys must be between 1 and 63 characters long and must conform
  to the following regular expression: `[a-z]([-a-z0-9]*[a-z0-9])?`.
  Label values must be between 0 and 63 characters long and must conform
  to the regular expression `([a-z]([-a-z0-9]*[a-z0-9])?)?`.
  No more than 64 labels can be associated with a given resource.
  See https://goo.gl/xmQnxf for more information on and examples of
  labels.
  If you plan to use labels in your own code, please note that
  additional characters may be allowed in the future. And so you are
  advised to use an internal label representation, such as JSON, which
  doesn't rely upon specific characters being disallowed. For example,
  representing labels as the string: name + "_" + value would prove
  problematic if we were to allow "_" in a future release.
  An object containing a list of "key": value pairs.
  Example: { "name": "wrench", "mass": "1.3kg", "count": "3" }.

#### Label
Set the `i_label` property when attempting to set primary key
of this object. The primary key will always be referred to by the initials of
the resource followed by "_label"

### gspanner_database
A Cloud Spanner Database which is hosted on a Spanner instance.


#### Example

```ruby
gspanner_instance 'my-spanner' do
  action :create
  display_name 'My Spanner Instance'
  node_count 2
  labels({
      'cost-center' => 'ti-1700004'
  })
  config 'regional-us-central1'
  project ENV['PROJECT']
  credential 'mycred'
end

gspanner_database 'webstore' do
  action :create
  instance 'my-spanner'
  extra_statements [
    'CREATE TABLE customers (
       customer_id INT64 NOT NULL,
       last_name STRING(MAX)
     ) PRIMARY KEY (customer_id)'
  ]
  project ENV['PROJECT']
  credential 'mycred'
end

```

#### Reference

```ruby
gspanner_database 'id-for-resource' do
  extra_statements [
    string,
    ...
  ]
  instance         reference to gspanner_instance
  name             string
  project          string
  credential       reference to gauth_credential
end
```

#### Actions

* `create` -
  Converges the `gspanner_database` resource into the final
  state described within the block. If the resource does not exist, Chef will
  attempt to create it.
* `delete` -
  Ensures the `gspanner_database` resource is not present.
  If the resource already exists Chef will attempt to delete it.

#### Properties

* `name` -
  A unique identifier for the database, which cannot be changed after
  the instance is created. Values are of the form
  projects/<project>/instances/[a-z][-a-z0-9]*[a-z0-9]. The final
  segment of the name must be between 6 and 30 characters in length.

* `extra_statements` -
  An optional list of DDL statements to run inside the newly created
  database. Statements can create tables, indexes, etc. These statements
  execute atomically with the creation of the database: if there is an
  error in any statement, the database is not created.

* `instance` -
  Required. The instance to create the database on.

#### Label
Set the `d_label` property when attempting to set primary key
of this object. The primary key will always be referred to by the initials of
the resource followed by "_label"

[google-gauth]: https://supermarket.chef.io/cookbooks/google-gauth
