---
title: Configure Service Connections for Ruby
---

_This page assumes that you are using cf v6._

After you create a service instance and bind it to an application, you must
configure the application to connect to the service.

## <a id='cf-app-utils'></a>Query `VCAP_SERVICES` with cf-app-utils ##

`cf-apps-utils` is a gem that allows your application to search for credentials
from VCAP_SERVICES by name, tag, or label.

* [cf-app-utils-ruby](https://github.com/cloudfoundry/cf-app-utils-ruby)

## <a id='vcap-services-defines-database-url'></a>`VCAP_SERVICES` defines `DATABASE_URL`

At runtime, every Ruby application - Rails and non-Rails - is provided a `DATABASE_URL` environment variable which is populated based on the [`VCAP_SERVICES` environment variable](../../devguide/deploy-apps/environment-variable.html#VCAP-SERVICES).

For example:

    VCAP_SERVICES =
    {
      "elephantsql": [
        {
          "name": "elephantsql-c6c60",
          "label": "elephantsql",
          "credentials": {
            "uri": "postgres://exampleuser:examplepass@babar.elephantsql.com:5432/exampledb"
          }
        }
      ]
    }

The `DATABASE_URL` variable will be:

    DATABASE_URL = postgres://exampleuser:examplepass@babar.elephantsql.com:5432/exampledb

Recognition of the details used to populate `DATABASE_URL` is based on the structure of `VCAP_SERVICES`. Any service containing a JSON object of the following form will be recognised by Cloud Foundry as a candidate for `DATABASE_URL`:

    {
      "some-service": [
        {
          "credentials": {
            "uri": "<some database URL>"
          }
        }
      ]
    }

If there are multiple candidates, the *first* candidate is used.

## <a id='rails-applications-have-autoconfigured-database-yml'></a>Rails Applications Have Auto-Configured `database.yml`

During staging the Ruby buildpack replaces your `database.yml` with one based on the `DATABASE_URL` variable.

If you supply a `database.yml` file, *it is not used* and will be overwritten during staging.

## <a id='configuring-non-rails-applications'></a>Configuring non-Rails Applications

Non-Rails applications can also see the `DATABASE_URL` variable.

If you have more than one service with credentials, only the first will be populated into `DATABASE_URL`. To access other credentials, you can inspect the `VCAP_SERVICES` environment variable.

~~~ruby
vcap_services = JSON.parse(ENV['VCAP_SERVICES'])
~~~

Use the hash key for the service to obtain the connection credentials
from `VCAP_SERVICES`.

- For services that use the [v2 Service Broker API](../../services/api.html), the hash key is the name of the service.

- For services that use the [v1 Service Broker API](../../services/api-v1.html), the hash key is formed by combining
the service provider and version, in the format PROVIDER-VERSION.

  For example, given a service provider "p-mysql" with version "n/a", the hash key is
`p-mysql-n/a`.

## <a id='migrate'></a>Seed or Migrate Database ##

Before you can use your database the first time, you must create and populate
or migrate it. For more information, see [Migrate a Database on Cloud Foundry](../../devguide/services/migrate-db.html).

## <a id='troubleshooting'></a>Troubleshooting ##

If you have trouble connecting to your service, run the `cf logs` command to view log messages for your application. The results of the `cf files my_app logs/env.log` command includes the value of the `VCAP_SERVICES` environment variable.

Example:

<pre class="terminal">
  $ cf files my_app logs/env.log
  Getting files for app my_app in or my_org / space my_space as a.user@example.com...
OK

TMPDIR=/home/vcap/tmp
VCAP_APP_PORT=64585
USER=vcap
VCAP_APPLICATION={"instance_id":"7dd14302a0804727a036b3b3f55300dc","instance_index":0,"host":"0.0.0.0",
"port":64585,"started_at":"2014-01-31 21:53:34 +0000","started_at_timestamp":1391205214,
"start":"2014-01-31 21:53:34 +0000","state_timestamp":1391205214,
"limits":{"mem":512,"disk":1024,"fds":16384},"application_version":"c1901bd3-ad2a-40f5-a8fd-204a901d038e",
"application_name":"my_app","application_uris":["my_app.example.com"],
"version":"c1901bd3-ad2a-40f5-a8fd-204a901d038e","name":"my_app","uris":["my_app.example.com"],"users":null}
PATH=/bin:/usr/bin
PWD=/home/vcap
VCAP_SERVICES={"p-mysql-n/a":[{"name":"p-mysql","label":"p-mysql-n/a","tags":["postgres","postgresql",
"relational"],"plan":"small_plan","credentials":{"uri":
"postgres://lrraxnih:eBawTKceuvKDGZycBvYUv5Bd6B-X1m4a9t@sample.p-mysqlprovider.com:5432/lraaxnih"}}]}
SHLVL=1
HOME=/home/vcap/app
PORT=64585
VCAP_APP_HOST=0.0.0.0
DATABASE_URL=postgres://lrraxnih:eBawTKceuvKDGZycBvYUv5Bd6B-X1m4a9t@sample.p-mysqlprovider.com:5432/lraaxnih
MEMORY_LIMIT=512m
_=/usr/bin/env
</pre>

If you encounter the error "A fatal error has occurred. Please see the Bundler
troubleshooting documentation," update your version of bundler and run `bundle
install` again.


<pre class="terminal">
  $ gem update bundler
  $ gem update --system
  $ bundle install
</pre>
