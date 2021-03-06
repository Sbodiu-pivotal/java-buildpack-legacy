# Cloud Foundry WebLogic Buildpack
[![Build Status](https://travis-ci.org/pivotal-cf/weblogic-buildpack.svg?branch=master)](https://travis-ci.org/pivotal-cf/weblogic-buildpack)
[![Dependency Status](https://gemnasium.com/pivotal-cf/weblogic-buildpack.svg)](https://gemnasium.com/pivotal-cf/weblogic-buildpack)
[![Code Climate](https://codeclimate.com/github/pivotal-cf/weblogic-buildpack/badges/gpa.svg)](https://codeclimate.com/github/pivotal-cf/weblogic-buildpack/feed)
[![Code Climate](https://codeclimate.com/github/pivotal-cf/weblogic-buildpack/badges/coverage.svg)](https://codeclimate.com/github/pivotal-cf/weblogic-buildpack/feed)

The `weblogic-buildpack` is a custom [Cloud Foundry] buildpack, based on a fork of the [Java-Buildpack][], for running JEE applications with WebLogic Server as container on Cloud Foundry.

The [Java-Buildpack] itself is a [Cloud Foundry][] buildpack for running JVM-based applications.  It is designed to run many JVM-based applications ([Grails][], [Groovy][], Java Main, [Play Framework][], [Spring Boot][], and Servlet) with no additional configuration, but supports configuration of the standard components, and extension to add custom components.

## Usage

To use this buildpack specify the URI of the repository when pushing an application to Cloud Foundry:

```bash
cf push <APP-NAME> -p <ARTIFACT> -b https://github.com/pivotal-cf/weblogic-buildpack.git
```

## Configuration and Extension
The buildpack supports extension through the use of Git repository forking. The easiest way to accomplish this is to use [GitHub's forking functionality][] to create a copy of this repository.  Make the required extension changes in the copy of the repository. Then specify the URL of the new repository when pushing Cloud Foundry applications. If the modifications are generally applicable to the Cloud Foundry community, please submit a [pull request][] with the changes.

Buildpack configuration can be overridden with an environment variable matching the configuration file you wish to override minus the `.yml` extension and with a prefix of `JBP_CONFIG`. The value of the variable should be valid inline yaml. For example, to change the default version of Java to 7 and adjust the memory heuristics apply this environment variable to the application.

```cf set-env my-application JBP_CONFIG_OPEN_JDK_JRE '[jre: {version: 1.7.0_+}, memory_calculator: {memory_heuristics: {heap: 85, stack: 10}}]'```

If the key or value contains a special character such as `:` it should be escaped with double quotes. For example, to change the default repository path for the buildpack.

```cf set-env my-application JBP_CONFIG_REPOSITORY '[ default_repository_root: "http://repo.example.io" ]'```

Environment variable can also be specified in the applications `manifest` file. See the [Environment Variables][] documentation for more information.

To learn how to configure various properties of the buildpack, follow the "Configuration" links below. More information on extending the buildpack is available [here](docs/extending.md).

## Additional Documentation
* [Design](docs/design.md)
* [Security](docs/security.md)
* Standard Containers
	* [Java Main](docs/container-java_main.md) ([Configuration](docs/container-java_main.md#configuration))
	* [WebLogic](docs/container-wls.md) ([Configuration](docs/container-wls.md#configuration))
* Standard Frameworks
	* [Java Options](docs/framework-java_opts.md) ([Configuration](docs/framework-java_opts.md#configuration))
* Standard JREs
	* [Oracle](docs/jre-oracle_jre.md) ([Configuration](docs/jre-oracle_jre.md#configuration))
* [Extending](docs/extending.md)
	* [Application](docs/extending-application.md)
	* [Droplet](docs/extending-droplet.md)
	* [BaseComponent](docs/extending-base_component.md)
	* [VersionedDependencyComponent](docs/extending-versioned_dependency_component.md)
	* [ModularComponent](docs/extending-modular_component.md)
	* [Caches](docs/extending-caches.md) ([Configuration](docs/extending-caches.md#configuration))
	* [Logging](docs/extending-logging.md) ([Configuration](docs/extending-logging.md#configuration))
	* [Repositories](docs/extending-repositories.md) ([Configuration](docs/extending-repositories.md#configuration))
	* [Utilities](docs/extending-utilities.md)
* [Debugging the Buildpack](docs/debugging-the-buildpack.md)
* [Buildpack Modes](docs/buildpack-modes.md)
* Related Projects
	* [Java Buildpack Dependency Builder](https://github.com/cloudfoundry/java-buildpack-dependency-builder)
	* [Java Test Applications](https://github.com/cloudfoundry/java-test-applications)
	* [Java Buildpack System Tests](https://github.com/cloudfoundry/java-buildpack-system-test)

## Building Packages
The buildpack can be packaged up so that it can be uploaded to Cloud Foundry using the `cf create-buildpack` and `cf update-buildpack` commands.  In order to create these packages, the rake `package` task is used.

### Online Package
The online package is a version of the buildpack that is as minimal as possible and is configured to connect to the network for all dependencies.  This package is about 50K in size.  To create the online package, run:

```bash
bundle install
bundle exec rake package
...
Creating build/weblogic-buildpack-cfd6b17.zip
```

### Offline Package
The offline package is a version of the buildpack designed to run without access to a network.  It packages the latest version of each dependency (as configured in the [`config/` directory][]) and [disables `remote_downloads`][]. This package is about 380M in size.  To create the offline package, use the `OFFLINE=true` argument:

To pin the version of dependencies used by the buildpack to the ones currently resolvable use the `PINNED=true` argument. This will update the [`config/` directory][] to contain exact version of each dependency instead of version ranges.

```bash
bundle install
bundle exec rake package OFFLINE=true PINNED=true
...
Creating build/weblogic-buildpack-offline-cfd6b17.zip
```

### Package Versioning
Keeping track of different versions of the buildpack can be difficult.  To help with this, the rake `package` task puts a version discriminator in the name of the created package file.  The default value for this discriminator is the current Git hash (e.g. `cfd6b17`).  To change the version when creating a package, use the `VERSION=<VERSION>` argument:
```bash
bundle install
bundle exec rake package VERSION=2.1
...
Creating build/weblogic-buildpack-2.1.zip
```

## Running Tests
To run the tests, do the following:

```bash
bundle install
bundle exec rake
```

[Running Cloud Foundry locally][] is useful for privately testing new features.

## Contributing
[Pull requests][] are welcome; see the [contributor guidelines][] for details.

## License
This buildpack is released under version 2.0 of the [Apache License][].

[Apache License]: http://www.apache.org/licenses/LICENSE-2.0
[Cloud Foundry]: http://www.cloudfoundry.com
[contributor guidelines]: CONTRIBUTING.md
[disables `remote_downloads`]: docs/extending-caches.md#configuration
[Environment Variables]: http://docs.cloudfoundry.org/devguide/deploy-apps/manifest.html#env-block
[GitHub's forking functionality]: https://help.github.com/articles/fork-a-repo
[Grails]: http://grails.org
[Groovy]: http://groovy.codehaus.org
[Installing Cloud Foundry on Vagrant]: http://blog.cloudfoundry.com/2013/06/27/installing-cloud-foundry-on-vagrant/
[Play Framework]: http://www.playframework.com
[pull request]: https://help.github.com/articles/using-pull-requests
[Pull requests]: http://help.github.com/send-pull-requests
[Spring Boot]: http://projects.spring.io/spring-boot/
[java-buildpack]: http://github.com/cloudfoundry/java-buildpack/
[Oracle WebLogic Application Server]: http://www.oracle.com/technetwork/middleware/weblogic/overview/index.html
[bosh-lite]: http://github.com/cloudfoundry/bosh-lite/
[Pivotal Web Services Marketplace]: http://docs.run.pivotal.io/marketplace/services/
[User Provided Services]: http://docs.run.pivotal.io/devguide/services/user-provided.html
[Linux 64 bit JRE]: http://javadl.sun.com/webapps/download/AutoDL?BundleId=83376
[WebLogic Server]: http://www.oracle.com/technetwork/middleware/weblogic/downloads/index.html
[limited footprint]: http://docs.oracle.com/middleware/1212/wls/START/overview.htm#START234
[syslog drain endpoint like Splunk]: http://www.youtube.com/watch?v=rk_K_AAHEEI
