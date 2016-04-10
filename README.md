# nginx-conf-helper
This repository is to help write nginx config easily.

## Why I create this repo?
Nginx is a good software, but somehow I offern forgot about the config.
It's better to write it into somewhere in case of forget again.

## Install Nginx
- Debian

	```
	$ sudo apt-get install nginx
	```

## Reverse proxy

### For basic HTTP reverse proxy.

```
server {
  listen 80;
  server_name jenkins.domain.com;

  location / {
    proxy_set_header        Host $host;
    proxy_set_header        X-Real-IP $remote_addr;
    proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header        X-Forwarded-Proto $scheme;

    # Fix the “It appears that your reverse proxy set up is broken" error.
    proxy_pass          http://localhost:8080;
    proxy_read_timeout  90;

    # Fix the "Location that when 302 or 301"
    proxy_redirect      http://localhost:8080 https://jenkins.domain.com;

    # Enable websocket support
    proxy_http_version  1.1;
    proxy_set_header    Upgrade $http_upgrade;
    proxy_set_header    Connection "upgrade";
  }
}
```

### For complex HTTPS.

Redirect 80 request

```
server {
  listen 80;
  return 301 https://$host$request_uri;
}
```

For 443 port

```
server {
  listen 443;
  server_name jenkins.domain.com;

  ssl on;
  ssl_certificate           /etc/nginx/cert.crt;
  ssl_certificate_key       /etc/nginx/cert.key;

  location / {

    proxy_set_header        Host $host;
    proxy_set_header        X-Real-IP $remote_addr;
    proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header        X-Forwarded-Proto $scheme;

    # Fix the “It appears that your reverse proxy set up is broken" error.
    proxy_pass          http://localhost:8080;
    proxy_read_timeout  90;

    proxy_redirect      http://localhost:8080 https://jenkins.domain.com;
  }
}
```

## Generate configuration example
Quickly generate configuation file `jenkins.conf`

1. create file `_config.yml`

	```yaml
	jenkins:
	  server_name: jenkins.domain.dom
	  proxy_pass: http://localhost:8080
	foo:
	  server_name:
        - foo.io
        - foo.org
	  proxy_pass: http://localhost:8081
	```

2. convert to `jenkins.conf`

	```
	python genconf.py _config.yml
	```

## Reference
* <http://nginx.org/en/docs/beginners_guide.html>
* <https://www.digitalocean.com/community/tutorials/how-to-configure-nginx-with-ssl-as-a-reverse-proxy-for-jenkins>

## LICENSE
Under [MIT](LICENSE)
