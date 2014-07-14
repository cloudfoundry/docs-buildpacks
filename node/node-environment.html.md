---
title: Environment Variables Defined by the Node Buildpack
---
_This page assumes that you are using cf v6._

When you use the Node buildpack, you get three Node-specific environment
variables in addition to the regular [Cloud Foundry environment variables]
(https://github.com/cloudfoundry/docs-dev-guide/blob/master/deploy-apps/environment-variable.html.md).

* `BUILD_DIR` — The directory into which Node.js is copied each time a Node.js application is run.

* `CACHE_DIR` — The directory that Node.js uses for caching.

* `PATH` — The system path used by Node.js:

    `PATH=/home/vcap/app/bin:/home/vcap/app/node_modules/.bin:/bin:/usr/bin`
