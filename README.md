# sfRestWebServicePlugins

The `sfRestWebServicePlugin` offers an easy interface for REST API based on your domain model.

## Installation and configuration

### Installation

Use the default plugin installer procedure

    php symfony plugin:install sfRestWebServicePlugin

then enable the plugin in your projectConfiguration class ( remember it needs the `sfDoctrinePlugin` enabled too, cause the services are based on your Doctrine schema ):

    public function setup()
    {
      $this->enablePlugins('sfDoctrinePlugin');
      $this->enablePlugins('sfRestWebServicePlugin');
    }

Last step, enable the module in the settings.yml of the application you want the webservices to be exposed into:

    all:
      ...
      enabled_modules:        [sfRestWebService]
      ...

### Configuration

You can - obviously - override and extends plugin's classes by creating them in your application's module directory.

The `sfRestWebServicePlugin` is based on the sfRestWebService module bundled with the plugin, so you only need to replicate the module on your application:

    $ mkdir apps/myApp/modules/sfRestWebService

For example, to override a template you will only need to create it on your application at the path:

`apps/myApp/modules/sfRestWebService/templates/errorSuccess.json.php`

The **core** configuration on the module lies in the config.yml that you have to override locally:

    $ touch apps/myApp/modules/sfRestWebService/config/config.yml

.config yaml ( you can override it ):
    all:
      protected: true
      allowed: [127.0.0.1]
      protectedRoute: secure
      services:
        name:
          model:  user
          methodForQuery: findActives
          states: [GET, PUT]

Here's a brief explanation for every configuration parameter:

    all:  The environment

    protected:  boolean, the webservices are protected or not?

    allowed:  if the webservices are protected, specify a YAML array of IP addresses that can access the services

    protectedRoute:  sets the route that non-allowed IP addresses will be redirected to

    services:  an array of single services configurations

    name:  the service name ( used in the service URL )

    model:  the model of the service

    methodForQuery:  a method for GET requests. If not specified, doctrine will do a `->createQuery()->execute()`

    states:  allowed request states ( PUT, POST, GET, DELETE ). If not specified, all state are allowed

If you turn on authentication, **remember to specify a secure route**.
If you have module `default` enabled, the route can be `secure` ( which is the name of the `default/secure` route ).

## Requirements

This plugins requires PHP's `short open tags` parameter set to `OFF`.
It would not be such a difficult matter to make the plugin work also with `short open tags` enabled, the point is that you shouldn't work this way.

## A specification

Since **PHP** sucks in so many ways handling PUT requests this plugin handles them with symfony's native REST architecture ( so, not not real PUT requests, but requests with the additional parametere `sf_method` set to PUT ).