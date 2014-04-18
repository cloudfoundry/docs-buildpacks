---
title: Tips for Java Developers
---
_This page assumes you are using cf v6._

Cloud Foundry can deploy a number of different JVM-based artifact types. For a more detailed explanation of what it supports, please see the [Java Buildpack documentation][d].

## Java Client Library  ##

The Cloud Foundry Client Library provides a Java API for interacting with a Cloud Foundry instance. This library, `cloudfoundry-client-lib`, is used by the Cloud Foundry Maven plugin, the Cloud Foundry Gradle plugin, the [Cloud Foundry STS integration](./sts.html), and other Java-based tools.

For information about using this library, see the [Java Cloud Foundry Library](./java-client.html) page.

## <a id='grails'></a>Grails ##
Grails packages applications into WAR files for deployment into a Servlet container.
To build the WAR file and deploy it, run the following:

<pre class="terminal">
$ grails prod war
$ cf push &lt;application-name&gt; -p target/&lt;application-name&gt;-&lt;application-version&gt;.war
</pre>

## <a id='groovy'></a>Groovy ##
Groovy applications based on both [Ratpack][r] and a simple collection of files are supported.

### <a id='ratpack'></a>Ratpack ###
Ratpack packages applications into two different styles; Cloud Foundry supports the `distZip` style. To build the ZIP and deploy it, run the following:

<pre class="terminal">
$ gradle distZip
$ cf push &lt;application-name&gt; -p build/distributions/&lt;application-name&gt;.zip
</pre>

### <a id='raw-groovy'></a>Raw Groovy ###
 Groovy applications that are made up of a [single entry point][g] plus any supporting files can be run without any other work. To deploy them, run the following:

<pre class="terminal">
$ cf push &lt;application-name&gt;
</pre>

## <a id='java-main'></a>Java Main ##
Java applications with a `main()` method can be run provided that they are packaged as [self-executable JARs][j].

### <a id='java-main-maven'></a>Maven ###
A Maven build can create a self-exectuable JAR. To build and deploy the JAR, run the following:

<pre class="terminal">
	$ mvn package
	$ cf push &lt;application-name&gt; -p target/&lt;application-name&gt;-&lt;application-version&gt;.jar
</pre>

### <a id='java-main-gradle'></a>Gradle ###
A Gradle build can create a self-exectuable JAR. To build and deploy the JAR, run the following:

<pre class="terminal">
	$ gradle build
	$ cf push &lt;application-name&gt; -p build/libs/&lt;application-name&gt;-&lt;application-version&gt;.jar
</pre>

## <a id='play-framework'></a>Play Framework ##
 The [Play Framework][p] packages applications into two different styles; Cloud Foundry supports both the `staged` and `dist` styles. To build the `dist` style and deploy it, run the following:

<pre class="terminal">
$ play dist
$ cf push &lt;application-name&gt; -p target/universal/&lt;application-name&gt;-&lt;application-version&gt.zip
</pre>

## <a id='spring-boot-cli'></a>Spring Boot CLI ##
[Spring Boot][s] can run applications [comprised entirely of POGOs][b]. To deploy then, run the following:

<pre class="terminal">
$ spring grab *.groovy
$ cf push &lt;application-name&gt;
</pre>

## <a id='servlet'></a>Servlet ##
Java applications can be packaged as Servlet applications.

### <a id='servlet-maven'></a>Maven ###
A Maven build can create a Servlet WAR. To build and deploy the WAR, run the following:

<pre class="terminal">
$ mvn package
$ cf push &lt;application-name&gt; -p target/&lt;application-name&gt;-&lt;application-version&gt;.war
</pre>


### <a id='servlet-gradle'></a>Gradle ###
A Gradle build can create a Servlet WAR. To build and deploy the JAR, run the following:

<pre class="terminal">
$ gradle build
$ cf push &lt;application-name&gt; -p build/libs/&lt;application-name&gt;-&lt;application-version&gt;.war
</pre>

## <a id='services'></a>Binding to Services ##
Information about binding apps to services can be found on the following pages:

* [Service Bindings for Grails Applications](./grails-service-bindings.html)
* [Service Bindings for Play Framework Applications](./play-service-bindings.html)
* [Service Bindings for Spring Applications](./spring-service-bindings.html)

-----

[b]: https://github.com/cloudfoundry/java-buildpack/blob/master/docs/container-spring_boot_cli.md
[d]: https://github.com/cloudfoundry/java-buildpack#additional-documentation
[g]: https://github.com/cloudfoundry/java-buildpack/blob/master/docs/container-groovy.md
[j]: https://github.com/cloudfoundry/java-buildpack/blob/master/docs/container-java_main.md
[p]: http://www.playframework.com
[r]: http://www.ratpack.io
[s]: http://projects.spring.io/spring-boot/

## <a id='buildpack'></a>Java Buildpack ##

For detailed information about using, configuring, and extending the Cloud Foundry
Java buildpack, see <https://github.com/cloudfoundry/java-buildpack>.

## <a id='design'></a>Design ##
The Java Buildpack is designed to convert artifacts that run on the JVM into
executable applications. It does this by identifying one of the supported artifact
types (Grails, Groovy, Java, Play Framework, Spring Boot, and Servlet) and downloading
all additional dependencies needed to run. The collection of services bound to the
application is also analyzed and any dependencies related to those services are also
downloaded.

As an example, pushing a WAR file that is bound to a PostgreSQL database and New Relic (for performance monitoring) would result in the following:


<pre class="terminal">
Initialized empty Git repository in /tmp/buildpacks/java-buildpack/.git/
-----> Java Buildpack source: https://github.com/cloudfoundry/java-buildpack#0928916a2dd78e9faf9469c558046eef09f60e5d
-----> Downloading Open Jdk JRE 1.7.0_51 from http://.../openjdk/lucid/x86_64/openjdk-1.7.0_51.tar.gz (0.0s)
       Expanding Open Jdk JRE to .java-buildpack/open_jdk_jre (1.9s)
-----> Downloading New Relic Agent 3.4.1 from http://.../new-relic/new-relic-3.4.1.jar (0.4s)
-----> Downloading Postgresql JDBC 9.3.1100 from http://.../postgresql-jdbc/postgresql-jdbc-9.3.1100.jar (0.0s)
-----> Downloading Spring Auto Reconfiguration 0.8.7 from http://.../auto-reconfiguration/auto-reconfiguration-0.8.7.jar (0.0s)
       Modifying /WEB-INF/web.xml for Auto Reconfiguration
-----> Downloading Tomcat 7.0.50 from http://.../tomcat/tomcat-7.0.50.tar.gz (0.0s)
       Expanding Tomcat to .java-buildpack/tomcat (0.1s)
-----> Downloading Buildpack Tomcat Support 1.1.1 from http://.../tomcat-buildpack-support/tomcat-buildpack-support-1.1.1.jar (0.1s)
-----> Uploading droplet (57M)
</pre>

## <a id='configuration'></a>Configuration ##
The buildpack is designed such that it will "just work" without any configuration. If
you are new to Cloud Foundry you should make your first attempts without modifying the
buildpack configuration. However, if you find that some configuration is required, it
can be done with [a fork of the buildpack][f].

## <a id='java-apps'></a>Java and Grails Best Practices ##

### <a id='jdbc'></a>Provide JDBC driver ###

The Java buildpack does not bundle a JDBC driver with your application.
If your application will access a SQL RDBMS, include the appropriate driver in
your application.

### <a id='memory'></a>Allocate Sufficient Memory ###

If you do not allocate sufficient memory to a Java application when you deploy
it, it may fail to start, or be killed by Cloud Foundry.
Allocate enough memory to allow for Java heap, PermGen, thread stacks, and JVM
overhead.
The Java buildpack allocates 10% of the memory limit to PermGen.
Memory-related JVM options are configured in `config/openjdk.yml`
(https://github.com/cloudfoundry/java-buildpack/blob/master/config/openjdk.yml).
When your application is running, you can use the `cf app APP_NAME` command to
see memory utilization.

### <a id='upload'></a>Troubleshoot Failed Upload ###

If your application fails to upload when you push it to Cloud Foundry, it may be
for one of the following reasons:

* WAR is too large: An upload may fail due to the size of the WAR file.
Cloud Foundry testing indicates WAR files as large as 250 MB upload
successfully.
If a WAR file larger than that fails to upload, it may be a result of the file
size.

* Connection issues: Application uploads can fail if you have a slow Internet
connection, or if you upload from a location that is very remote from the target
Cloud Foundry instance.
If an application upload takes a long time, your authorization token can expire
before the upload completes.
A workaround is to copy the WAR to a server that is closer to the Cloud Foundry
instance, and push it from there.

* Out-of-date cf client: Upload of a large WAR large is faster and hence less
likely to fail if you are using a recent version of cf.
If you are using an older version of the cf client to upload a large, and having
problems, try updating to the latest version of cf.

* Incorrect WAR targeting: By default, `cf push` uploads everything in the
current directory.
For a Java application, a plain `cf push` push will upload source code and other
unnecessary files, in addition to the WAR.
When you push a Java application, specify the path to the WAR:

  <pre class="terminal">
  cf push APP_NAME -p PathToWAR
  </pre>

 You can determine whether or not the path was specified for a previously pushed
application by looking at the application deployment manifest, `manifest.yml`.
If the `path` attribute specifies the current directory, the manifest will
include a line like this:

  ```
    path: .
  ```
 To re-push just the WAR, either:

 * Delete `manifest.yml` and push again, specifying the location of the WAR
using the `-p` flag, or

 * Edit the `path` argument in `manifest.yml` to point to the WAR, and re-push
the application.

### <a id='slow-start'></a>Slow Starting Java or Grails Apps ###

It is not uncommon for a Java or Grails application to take a while to start.
This, in conjunction with the current Java buildpack implementation, can pose a
problem.
The Java buildpack sets the Tomcat `bindOnInit` property to "false", which
causes Tomcat to not listen for HTTP requests until the application is deployed.
If your application takes a long to time to start, the DEA's health check may
fail by checking application health before the application can accept requests.
A workaround is to fork the Java buildpack and change `bindOnInit` to "false" in
`resources/tomcat/conf/server.xml`, although this has the downside that the
application may appear to be running (as far as Cloud Foundry is concerned)
before it is ready to serve requests.


## <a id='extension'></a>Extension ##
The Java Buildpack is also designed to be easily extended. It creates abstractions for
[three types of components][a] (containers, frameworks, and JREs) in order to allow
users to easily add (and contribute back) functionality. In addition to these
abstractions, there are a number of [utility classes][e] for simplifying typical
buildpack behaviors.

As an example, the New Relic framework looks like the following:

```ruby
class NewRelicAgent < JavaBuildpack::Component::VersionedDependencyComponent

  # @macro base_component_compile
  def compile
    FileUtils.mkdir_p logs_dir

    download_jar
    @droplet.copy_resources
  end

  # @macro base_component_release
  def release
    @droplet.java_opts
    .add_javaagent(@droplet.sandbox + jar_name)
    .add_system_property('newrelic.home', @droplet.sandbox)
    .add_system_property('newrelic.config.license_key', license_key)
    .add_system_property('newrelic.config.app_name', "'#{application_name}'")
    .add_system_property('newrelic.config.log_file_path', logs_dir)
  end

  protected

  # @macro versioned_dependency_component_supports
  def supports?
    @application.services.one_service? FILTER, 'licenseKey'
  end

  private

  FILTER = /newrelic/.freeze

  def application_name
    @application.details['application_name']
  end

  def license_key
    @application.services.find_service(FILTER)['credentials']['licenseKey']
  end

  def logs_dir
    @droplet.sandbox + 'logs'
  end

end
```

[a]: https://github.com/cloudfoundry/java-buildpack/blob/master/docs/design.md
[e]: https://github.com/cloudfoundry/java-buildpack/blob/master/docs/extending.md
[f]: https://github.com/cloudfoundry/java-buildpack#configuration-and-extension