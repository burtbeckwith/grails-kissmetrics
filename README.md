KISSmetrics Grails Plugin
=========================

# Introduction

The **KISSmetrics Plugin** allows you to integrate [KISSmetrics](http://www.kissmetrics.com) in your [Grails](http://grails.org) application.

It provides the following Grails artefacts:
* **KissmetricsService** - A server side service client to call [KISSmetrics APIs](http://support.kissmetrics.com/apis/specifications).
* **KissmetricsTagLib** - A collection of tags to use [KISSmetrics Javascript Library](http://support.kissmetrics.com/apis/javascript) in your GSPs.


# Installation

Declare the plugin dependency in the BuildConfig.groovvy file, as shown here:

```groovy
grails.project.dependency.resolution = {
		inherits("global") { }
		log "info"
		repositories {
				//your repositories
		}
		dependencies {
				//your dependencies
		}
		plugins {
				//here go your plugin dependencies
				runtime ':kissmetrics:0.1.0'
		}
}
```


# Config

Create a [KISSmetrics](http://www.kissmetrics.com) account, in order to get your own apiKey.

Add your KISSmetrics site apiKey to your _grails-app/conf/Config.groovy_:

```groovy
grails.plugin.kissmetrics.apiKey = {API_KEY}
```
By default the KISSmetrics will only be enabled for Production environments.  If you need it to be enabled for other environments, make sure that it is explicitly enabled in your configs.

```groovy
grails.plugin.kissmetrics.enabled = true
```


# Usage

## KissmetricsService

You can inject _kissmetricsService_ in any of your Grails artefacts (controllers, services...) in order to call [KISSmetrics APIs](http://support.kissmetrics.com/apis/specifications).

```groovy
def kissmetricsService

// Record an event
kissmetricsService.recordEvent('bob@bob.com', 'Signed up')

// Record an event and set properties
kissmetricsService.recordEvent('bob@bob.com', 'Signed up', [plan: 'Pro', amount: 99.95])

// Record a past event and set properties
kissmetricsService.recordEvent('bob@bob.com', 'Signed up', [plan: 'Pro', amount: 99.95], (new Date() - 10).time)

// Set properties
kissmetricsService.setProperties('bob@bob.com', [gender: 'male'])

// Alias identity
kissmetricsService.alias('bob@bob.com', 'bob')
```

An *async* version is available for each methods (to delegate call to a thread pool and execute it asynchronously) for better performances.

```groovy
// Alias identity
kissmetricsService.aliasAsync('bob', 'bob@bob.com')

// Record an event
kissmetricsService.recordEventAsync('bob@bob.com', 'Signed up')

// Set properties
kissmetricsService.setPropertiesAsync('bob@bob.com', [gender: 'male'])
```
HTTP client is based on [HTTPBuilder](http://groovy.codehaus.org/modules/http-builder).
You might want to surround service method calls in try/catch blocks to catch HttpResponseException.

```groovy
import groovyx.net.http.HttpResponseException

try {
    // Record an event
    kissmetricsService.recordEvent('bob@bob.com', 'Signed up')
} catch (HttpResponseException exception) {
    // default failure handler throws an exception:
    println "Unexpected response error: ${exception.statusCode}"
}
```

## KissmetricsTagLib

To use [KISSmetrics Javascript Library](http://support.kissmetrics.com/apis/javascript), you must first initialize it in page header (most probably in you layout GSP).

```jsp
<!DOCTYPE html>
<html>
<head>
    <kissmetrics:initJS/>
```

Once initialized, you can use [KISSmetrics Javascript Library](http://support.kissmetrics.com/apis/javascript) in your GSP views.

```jsp
<!-- Identity current user -->
<kissmetrics:identify id="bob@bob.com"/>

<!-- Record an event -->
<kissmetrics:record event="Signed Up"/>

<!-- Record an event and set properties -->
<kissmetrics:record event="Signed Up" properties="${[plan: 'Pro', amount: 99.95]}"/>

<!-- Set properties -->
<kissmetrics:set properties="${[gender: 'male']}"/>

<!-- Alias identity -->
<kissmetrics:alias id="bob@bob.com" to="bob"/>
```

It will generate the corresponding JS code that will be automatically deferred to page footer thanks to [Grails Resources framework](https://github.com/grails-plugins/grails-resources).


# Latest releases

* 2012-11-28 **V0.1.0** : initial release

# Bugs

To report any bug, please use the project [Issues](http://github.com/benorama/grails-kissmetrics/issues) section on GitHub.


# Beta status

This is a **beta release**.
