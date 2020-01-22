# nginx

[Lagoon `nginx` image Dockerfile](https://github.com/amazeeio/lagoon/blob/master/images/nginx/Dockerfile), based on official [`openresty/openresty` images](https://hub.docker.com/r/openresty/openresty/).

This Dockerfile is intended to be used as a base for any web servers within Lagoon.

## Lagoon & OpenShift adaptions

This image is prepared to be used on Lagoon which leverages OpenShift. There are therefore some things already done:

* Folder permissions are automatically adapted with [`fix-permissions`](https://github.com/sclorg/s2i-base-container/blob/master/core/root/usr/bin/fix-permissions)so this image will work with a random user, and therefore also on OpenShift.
* The files within `/etc/nginx/*` are parsed through [`envplate`](https://github.com/kreuzwerker/envplate) with a container-entrypoint.

## Included `nginx` configuration \(`static-files.conf`\)

{% hint style="warning" %}
By default `nginx` only serves static files - this can be used for static sites that don't require a database or php components: for example, static site generators like [Hugo](https://gohugo.io/), [Jekyll](https://jekyllrb.com/) or [Gatsby](https://www.gatsbyjs.org/).

If you need PHP, have a look at the `php-fpm` image and use `nginx` and `php-fpm` in tandem.
{% endhint %}

Build the content during the build process and inject it into the `nginx` container.

## Helpers

### `redirects-map.conf`

In order to create redirects, we have `redirects-map.conf` in place. This helps you to redirect marketing domains to sub-sites or do non-www to www redirects. **If you have a lot of redirects, we suggest having `redirects-map.conf` stored next to your code for easier maintainability.**

{% hint style="info" %}
If you just have a few redirects, there's a handy trick to create the redirects with a `RUN` command in your `nginx.dockerfile`.
{% endhint %}

Here's an example showing how to redirect `www.example.com` to `example.com` and preserve the request:

```bash
RUN echo "~^www.example.com http://example.com\$request_uri;" >> /etc/nginx/redirects-map.conf
```

To get more details about the various types of redirects that can be achieved, see the documentation within the [`redirects-map.conf`](https://github.com/amazeeio/lagoon/blob/master/images/nginx/redirects-map.conf) directly.

After you put the `redirects-map.conf` in place, you also need to include it in your `nginx.dockerfile` in order to get the configuration file into your build.

```bash
COPY redirects-map.conf /etc/nginx/redirects-map.conf
```

### Basic Authentication

If you want to protect your site via basic authentication, you can do this by defining the environment variables `BASIC_AUTH_USERNAME` and `BASIC_AUTH_PASSWORD` within your `.lagoon.env.environment` files. For further explanation on how to set up Environment Variables on Lagoon, [check here](../../using-lagoon/environment-variables.md).

## Environment Variables

Environment variables are meant to contain common information for the `nginx` container.

| Environment Variable | Default | Description |
| :--- | :--- | :--- |
| `BASIC_AUTH_USERNAME` | \(not set\) | Username for basic Authentication |
| `BASIC_AUTH_PASSWORD` | \(not set\) | Password for basic authentication \(unencrypted\) |
