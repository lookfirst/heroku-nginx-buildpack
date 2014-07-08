# A Heroku Nginx buildpack & builder

## Building binaries

* Install Vagrant and VirtualBox

```
mkdir /tmp/nginx
vagrant up
vagrant ssh
cd /vagrant
support/heroku-buildpack run
```

* Commit the builds directory.

## Using this buildpack for an app

Create or add this buildpack to an app on heroku.

```
heroku config:add BUILDPACK_URL=https://github.com/lookfirst/heroku-nginx-buildpack.git#voost
```

In the app, add a `index.html` and a `nginx.conf`. This becomes the `public` folder when
you push the app up to heroku.

## Custom configuration files

An example of the most basic `nginx.conf`:

```
worker_processes 1;
daemon off;

error_log /app/nginx/logs/error.log;
events { worker_connections 1024; }

http {
  access_log /app/nginx/logs/access.log combined;
  default_type application/octet-stream;
  include mime.types;
  sendfile on;
  gzip on;
  tcp_nopush on;
  keepalive_timeout 30;

  server {
    listen <%= ENV["PORT"] %>;
    server_name localhost;

    location ~ /\.ht { deny  all; }
    location / {
      root /app/public;
      index  index.html;
    }
  }
}
```
