# Makisu

A CLI utility for capturing, restoring, and cloning Heroku apps and their configured state based on templates. Think Cloudformation for Heroku.

## Background

When I started to work on
[dissociation](http://github.com/jayzes/dissociation), I realized that
there wasn't an easy way to snapshot and clone the configuration of an
Heroku app, including its addons, config vars, a database snapshot, etc.
While it's not possible to do that in a 100% automatable way (in particular, 
transferring the state of certain addons such as databases would be nearly 
impossible to do generically, and certain addons do require manual
configuration), the goal of this gem is to make that cloning process
work well enough to fire up and tear down cloned environments that are
*at least* good enough for acceptance testing usage.

What it can capture/clone/restore currently:

* Current revision (SHA) running in the app
* Current process scaling settings
* Configured addons and their levels
* A snapshot of the current Postrges database

What it doesn't capture
* Data stored in addon databases (Mongo, Redis, etc.)

## Installation

Add this line to your application's Gemfile:

    gem 'makisu'

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install makisu

## Usage

### Capturing the current state of a running app

    $ makisu capture <app name>

This will write the configuration for the specified app to
<appname>.json, unless an alternate path is specified with the -o
option.

### Restoring a captured state to a new app

    $ makisu restore <config file>

This will use the config file specified to boot a new app and restore 
it to the state captured in the config file. The name of the new app may
be specified using the -a option.

### Cloning an existing app

    $ makisu clone <app name>

This will clone the app specified to a new app with the same
configuration. It is essentially a shorthand for a capture followed by a
restore. The name of the new app may be specified using the -a option.

### Reifying an existing app

    $ makisu reify <app name> <config file>

This will modify the configuration of the app specified to match the configuration
specified in the config file. Note that this is potentially a
destructive action, so you may wish to be careful with it. By default,
the application SHA will be force-pushed, the state of the database will
be overridden, addons will be added, removed, upgraded, and downgraded
as appropriate, and processes will be rescaled. These behaviors may be
changed using the --skip-<action> directives - run `makisu help reify`
for details on how to use them.


## Contributing

1. Fork it
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create new Pull Request
