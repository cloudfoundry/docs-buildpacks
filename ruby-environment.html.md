---
title: Environment Variables Defined by the Ruby Buildpack
---

_This page assumes that you are using cf v6._

When you use the Ruby buildpack, you get three Ruby-specific environment
variables in addition to the regular [Cloud Foundry environment variables]
(../devguide/deploy-apps/environment-variable.html).

* `BUNDLE_BIN_PATH` — Location where Bundler installs binaries.

    `BUNDLE_BIN_PATH:/home/vcap/app/vendor/bundle/ruby/1.9.1/gems/bundler-1.3.2/bin/bundle`

* `BUNDLE_GEMFILE` — Path to application’s gemfile.

    `BUNDLE_GEMFILE:/home/vcap/app/Gemfile`

* `BUNDLE_WITHOUT` — This variable causes Cloud Foundry to skip installation
of gems in excluded groups.
Useful for Rails applications, where “assets” and “development” gem groups
typically contain gems that are not needed when the app runs in production.

    See [this]
    (http://blog.cloudfoundry.com/2012/10/02/polishing-cloud-foundrys-ruby-gem-support)
    blog post for more information.

    `BUNDLE_WITHOUT=assets`

* `DATABASE_URL` — The Ruby buildpack looks at the database\_uri for bound services to see if they
match known database types.
If there are known relational database services bound to the application, the
buildpack sets up the `DATABASE_URL` environment variable with the first one in
the list.

    If your application depends on DATABASE\_URL being set to the connection string
    for your service, and Cloud Foundry does not set it, you can set this variable
    manually.

    `$ cf set-env my_app_name DATABASE_URL mysql://b5d435f40dd2b2:ebfc00ac@us-cdbr-east-03.cleardb.com:3306/ad_c6f4446532610ab`

* `GEM_HOME` — Location where gems are installed.

    `GEM_HOME:/home/vcap/app/vendor/bundle/ruby/1.9.1`

* `GEM_PATH` — Location where gems can be found.

    `GEM_PATH=/home/vcap/app/vendor/bundle/ruby/1.9.1:`

* `RACK_ENV` — This variable specifies the Rack deployment environment: development,
deployment, or none.
This governs what middleware is loaded to run the application.

    `RACK_ENV=production`

* `RAILS_ENV` — This variable specifies the Rails deployment environment: development, test, or
production.
This controls which of the environment-specific configuration files will govern
how the application will be executed.

     `RAILS_ENV=production`

* `RUBYOPT` — This Ruby environment variable defines command-line options passed to Ruby
interpreter.

    `RUBYOPT: -I/home/vcap/app/vendor/bundle/ruby/1.9.1/gems/bundler-1.3.2/lib -rbundler/setup`
