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

    proxy_redirect      http://localhost:8080 https://jenkins.domain.com;
  }
}
```
## Reference
* <https://www.digitalocean.com/community/tutorials/how-to-configure-nginx-with-ssl-as-a-reverse-proxy-for-jenkins>

## LICENSE
Under [MIT](LICENSE)
