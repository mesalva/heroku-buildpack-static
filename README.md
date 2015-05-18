# heroku-buildpack-static
This is a discovery project for using a buildpack for handling static sites and single page web apps (I'm lookingat you Sam Phippen).

## Features
* serving static assets
* gzip on by default
* support for proxying backends
* error/access logs support in `heroku logs`

## Deploying
The directory structure expected is that you have a `public_html/` directory containing all your static assets.

1. Set the app to this buildpack: `$ heroku buildpacks:set git://github.com/hone/heroku-buildpack-static.git`.
2. Deploy: `$ git push heroku master`

### Configuration
You can configure different options for your static application by writing a `static.json` in the root folder of your application.

#### Root
This allows you to specify a different asset root for the directory of your application. For instance, if you're using ember-cli, it naturally builds a `dist/` directory, so you might want to use that intsead.

```json
{
  "root": "dist/"
}

```

By default this is set to `public_html/`

#### Clean URLs
For SEO purposes, you can drop the `.html` extension from URLs for say a blog site. This means users could go to `/foo` instead of `/foo.html`.


```json
{
  "clean_urls": true
}
```

By default this is set to `false`.

#### Proxy Backends
For single page web applications like Ember, it's common to back the application with another app that's hosted on Heroku. The down side of separating out these two applications is that now you have to deal with CORS. To get around this (but at the cost of some latency) you can have the static buildpack proxy apps to your backend at a mountpoint. For instance, we can have all the api requests live at `/api/` which actually are just requests to our API server.

```json
{
  "root": "public/",
  "clean_urls": true,
  "proxies": {
    "/api/": {
      "origin": "https://hone-ember-todo-rails.herokuapp.com/"
    }
  }
}
```