# Sudanses Archive website

The website for sudansesarchive.org

This site uses the [mnemonic-archive-http](https://github.com/sudansesarchive/mnemonic-archive-http) module which is a static website using metalsmith.  It generates static html pages, that pull in a react application for the dynamic elements. The react application calls the api for the database and statistics.


## Dependencies

```
git
node
npm
```

## Deploy

To Deploy this site: 

First install the necessary libraries by running:

```
npm install
```

Then clone the `mnemonic-archive-http` module and initial content:

```
npm run initial-setup
```

----------------------

Finally, copy `env.js.sample` to `env.js`, and build the site:

```
npm run build
```

This updates the content repository and builds the site to `dist`.

## Development

After the deploy steps worked well, running an `npm run dev`  should set up the development server at `http://localhost:8080`.

## Serving

`dist` generated by `npm run build` should then be served with nginx.  the following config works well:

```
server {
  listen 80;
  server_name sudansesarchive.org;
  client_max_body_size 20M;
  return 301 https://$host$request_uri;
}

server {
  listen 443 ssl;
  client_max_body_size 20M;
  server_name sudansesarchive.org ;

  # SSL STUFF

  rewrite ^(/.*)\.html(\?.*)?$ $1$2 ;
  rewrite ^/(.*)/$ /$1 ;

  root /PATH-TO-APPLICAION/dist;

  access_log /var/log/nginx/syarch-prd/access.log;
  error_log /var/log/nginx/syarch-prd/error.log;


  default_type "text/html";
  index index.html;
  try_files $uri/index.html $uri.html $uri/ $uri =404;

  location = /robots.txt {
    allow all;
    log_not_found off;
    access_log off;
  }

  # Block access to "hidden" files and directories whose names begin with a
  # period.
  location ~ (^|/)\. {
    return 403;
  }

  location ~* \.(jpg|jpeg|gif|png|ico|css|zip|tgz|gz|rar|bz2|doc|xls|pdf|ppt|txt|tar|wav|bmp|rtf|js|mp3|avi|mov|flv|swf|woff|ttf|html)$ {
    access_log off;
    expires max;
  }

  error_page 500 502 503 504 /50x.html;
  error_page 404 /404.html;
}
```
