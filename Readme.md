# A Heroku Nginx buildpack & builder

_For those of you wondering why the builds are no longer publically available, it's because Heroku no longer supports the Vulcan build system so I can no longer enable cloudflare for those builders and upload the files to their repo.  *In the coming days I will be simply uploading them to my website.*_

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

## Using this buildpack as is

Create or add this buildpack to an app on heroku.

```
heroku config:add BUILDPACK_URL=https://github.com/envygeeks/heroku-nginx-buildpack.git
```

In the app, add a `index.html` and a `nginx.conf`. This becomes the `public` folder when
you push the app up to heroku.

## Custom configuration files

This buildpack supports a custom configuration file by just adding `nginx.conf` to the `public` folder.
If it detects said file it will use it in place of the built-in nginx.conf and run it through the
same erb processor.  An example of the most basic `nginx.conf`:

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
