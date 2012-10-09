Description
===========

This is a fork of the Opscode [haproxy](https://github.com/opscode/cookbooks/tree/master/haproxy) cookbook (originally v.1.0.4), that has been modified to configure app load balancers from a data bag items.

Changes
=======
## v1.2.0:

* Got rid of dependence upon data bags for the definition of backend servers.  We now use an attribute named `backend_servers`.  Collection and the attributes of each element mirror those of the data bag.

## v1.1.0:
    
* Forked and modified to pull configuration data from a data bag.

## v1.0.4:

* [COOK-806] - load balancer should include an SSL option 
* [COOK-805] - Fundamental haproxy load balancer options should be configurable

## v1.0.3:

* [COOK-620] haproxy::app_lb's template should use the member cloud private IP by default

## v1.0.2:

* fix regression introduced in v1.0.1

## v1.0.1:

* account for the case where load balancer is in the pool

## v1.0.0:

* Use `node.chef_environment` instead of `node['app_environment']`

Requirements
============

## Platform

Tested on Ubuntu 8.10 and higher.

## Cookbooks:

Attributes
==========

* `node['haproxy']['incoming_port']` - sets the port on which haproxy listens
* `node['haproxy']['enable_admin']` - whether to enable the admin interface. default true. Listens on port 22002.
* `node['haproxy']['balance_algorithm']` - sets the load balancing algorithm; defaults to roundrobin.
* `node['haproxy']['member_max_connections']` - the maxconn value to be set for each app server
* `node['haproxy']['x_forwarded_for']` - if true, creates an X-Forwarded-For header containing the original client's IP address. This option disables KeepAlive.
* `node['haproxy']['enable_ssl']` - whether or not to create listeners for ssl, default false
* `node['haproxy']['ssl_incoming_port']` - sets the port on which haproxy listens for ssl, default 443
* `node['haproxy']['cookie_insert']` - sets the `cookie_insert` string if you want load balancing based on cookie insertion
* `node['haproxy']['cookie_prefix']` - sets the `cookie_prefix` string if you want to use cookie prefixing
* `node['haproxy']['backend_servers']` - a list of backend server configurations. These will be used to write the configuration file (see below).

Usage
=====

Use either the default recipe or the `app_lb` recipe.

When using the default recipe, modify the haproxy.cfg.erb file with listener(s) for your sites/servers.

A sample role, `haproxy_demo` might look like this:

    name "haproxy_demo"
    description "demo haproxy recipe"
    run_list(
      "recipe[haproxy::default]",
      "recipe[haproxy::app_lb]",
    )
    override_attributes(
      "haproxy" => {
        'incoming_port' => "80",
        'balance_algorithm' => "roundrobin",
        'backend_servers' => [
          {
            "hostname"        => "appserver1.example.com",
            "ipaddress"       => "192.168.0.1",
            "port"            => "8080",
            "proxy_weight"    => 1,
            "max_connections" => 100,
            "ssl_port"        => 443
          },
          {
            "hostname"        => "appserver2.example.com",
            "ipaddress"       => "192.168.0.2",
            "port"            => "8080",
            "proxy_weight"    => 1,
            "max_connections" => 100,
            "ssl_port"        => 443
          }
        ]
      }
    )

License and Author
==================

Author:: Joshua Timberman (<joshua@opscode.com>)
Author:: Brad Montgomery (<bmontgomery@coroutine.com>)
Author:: Tim Lowrimore (<tlowrimore@coroutine.com>)

Copyright:: 2009-2012, Opscode, Inc

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
