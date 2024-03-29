# Passenger NGINX

[![CI](https://github.com/jobscore/ansible-role-passenger-nginx/actions/workflows/ci.yml/badge.svg?branch=master)](https://github.com/jobscore/ansible-role-passenger-nginx/actions/workflows/ci.yml)

Ansible Role for installing Passenger and NGINX for Ruby apps

## Requirements

This role expects Ruby to be already installed on the target machine, the path for the ruby binary can be configured using the `passenger_ruby` variable

## Role Variables

### Passenger configuration

```
passenger_app_root: /mnt/app/public
```

The path of the public folder of your Rails app

```
passenger_app_env: production
```

The RAILS_ENV on which Passenger will run your Rails app

```
passenger_root: /usr/lib/ruby/vendor_ruby/phusion_passenger/locations.ini
```
The path for your passenger config root file, this file is not managed by this role, therefore you should only change this in case you have manually configured a passenger setup elsewhere

```
passenger_ruby: /usr/local/bin/ruby
```

The path for you Ruby binary

```
passenger_extra_config: ''
```

On this variable you can add/customize the passenger behavior for your apps(used on [mod-http-passenger.conf.j2](/templates/mod-http-passenger.conf.j2)), more info about passenger parameters for NGINX can be found [here](https://www.phusionpassenger.com/library/config/nginx/reference/).

### NGINX configuration

```
nginx_server_name: www.example.com
```

The server name that NGINX will use to listen for HTTP requests

```
nginx_user: "www-data"
```

The user that NGINX will use to run the server

```
nginx_extra_config: ''
```

Additional config params that you might want to add to the [nginx.conf](/templates/nginx.conf.j2) template on the root level.

```
nginx_events_extra_config: ''
```
Additional config params that you might want to add to the [nginx.conf](/templates/nginx.conf.j2) template under the **events** section.

```
nginx_http_extra_config: ''
```
Additional config params that you might want to add to the [nginx.conf](/templates/nginx.conf.j2) template under the **http** section.

```
nginx_vhost_config: |
  server {
    listen 80 default_server;
    server_name {{ nginx_server_name }};
    passenger_enabled on;
    passenger_app_env {{ passenger_app_env }};
    root {{ passenger_app_root }};
  }
```
NGINX configuration for the passenger app, the default configuration works for all apps, if you want to add/customize behavior you can edit this variable. You can use variables here as you were using a Jinja template.

```
nginx_worker_processes: "auto"
nginx_worker_connections: "768"
nginx_keepalive_timeout: "65"
```

Those values are used to configure their respective values in the [nginx.conf](/templates/nginx.conf.j2) template.

## Example Playbooks

``` yaml
- hosts: servers
  roles:
    - role: jobscore.ruby
    - role: jobscore.passenger_nginx
```

``` yaml
- hosts: servers
  pre_tasks:
    - apt:
      name: ruby-full
      state: present
  roles:
    - role: jobscore.passenger_nginx
      nginx_server_name: www.example.com
      passenger_extra_config: |
        passenger_max_pool_size 6;
        passenger_min_instances 6;
        passenger_pre_start {{ nginx_server_name }};
```

## License

[MIT](/LICENSE)

Author Information
------------------

This role was created by [Eric Magalhães](https://emagalha.es) and [Glauber Batista](https://glauberrbatista.dev) while working for [JobScore Inc](https://jobscore.com).
