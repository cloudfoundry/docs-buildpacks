---
title: Staticfile Buildpack
owner: Buildpacks
---

<strong><%= modified_date %></strong>

Use the Staticfile buildpack with front-end only web apps or demos.

You only need to create a `Staticfile` file for Cloud Foundry to detect this buildpack:

```
$ touch Staticfile
$ cf push my-site -m 64M
```

Why `-m 64M`? Your static assets will be served by [Nginx](http://nginx.com/) and it only requires 20M \[[reference](http://wiki.nginx.org/WhyUseIt)]. The `-m 64M` reduces the RAM allocation from the default 1G allocated to Cloud Foundry containers. In the future there may be a way for a buildpack to indicate its default RAM requirements; but not as of writing.

Configuration
=============

### Alternate root folder

By default, the buildpack will serve `index.html` and all other assets from the root folder of your project.

In many cases, you may have an alternate folder where your HTML/CSS/JavaScript files are to be served from, such as `dist/` or `public/`.

To configure the buildpack add the following line to your `Staticfile`:

```yaml
root: dist
```

### Basic authentication

Protect your website with a user/password configured via environment variables.

![Basic Authentication](../images/basicauth.png)

Convert the username / password to the required format: http://www.htaccesstools.com/htpasswd-generator/

For example, username `bob` and password `bob` becomes `bob:$apr1$DuUQEQp8$ZccZCHQElNSjrg.erwSFC0`.

Create a file in the root of your application `Staticfile.auth`. This becomes the `.htpasswd` file for nginx to project your site. It can include one or more user/password lines.

```
bob:$apr1$DuUQEQp8$ZccZCHQElNSjrg.erwSFC0
```

Push your application to apply changes to basic auth. Remove the file and push to disable basic auth.

### Directory Index

If your site doesn't have a nice `index.html`, you can configure `Staticfile` to display a Directory Index of other files; rather than show a relatively unhelpful 404 error.

![Directory Index](../images/directory-index.png)

Add a line to your `Staticfile` that begins with `directory:`

```
directory: visible
```

### Advanced Nginx configuration

You can customize the Nginx configuration further, by adding `nginx.conf` and/or `mime.types` to your root folder.

If the buildpack detects either of these files, they will be used in place of the built-in versions.

For information about using and extending the Staticfile buildpack in Cloud Foundry, see the [staticfile-buildpack GitHub repo](https://github.com/cloudfoundry/staticfile-buildpack).

You can find current information about this buildpack on the Staticfile
buildpack [release page](https://github.com/cloudfoundry/staticfile-buildpack/releases) in GitHub.
