==============
Caliper-python
==============

`Caliper-python` is a Python client package for `Caliper <http://imsglobal.org/>`_
that provides an implementation of the Caliper SensorAPI(tm).


Dependencies
============
To effectively use this `caliper-python` package, you will need to have Python,
pip, and setuptools installed. This package also depends on several third-party
packages:

* future -- (MIT licensed)

* oauthlib -- (3-clause BSD licensed)

* requests -- (Apache 2 licensed)

* tox -- (soft-dependency used only for tests, MIT licensed)

To work with this package (maintain, use, run tests) you'll need to have these
third-party packages in your local Python environment.

Which platform
--------------
We developed `caliper-python` primarily using the Python 3.4+ platform; however,
we've taken steps to make it portably usbale with Python 2.7 as well (using
the `future` package). Changes to the code base should ensure the tests run clean
under Python 3.4 *and* 2.7.


Build and install
=================
We built `caliper-python` to be packagable for loading from PyPi, or direct
from a source bundle, with `pip`.

If it's on PyPi, you can simply install it into your site-packages with::

  pip install caliper-python

Building and installing from a source bundle
--------------------------------------------
If you want to install it from a source bundle, then use these steps:

#. Clone the `repository from GitHub <https://github.com/IMSGlobal/caliper-python.git>`_
   onto your local machine.

#. Use pip to install the package as a writable source bundle; there's no need
   to do a package build step ahead of time::

     pip install -e caliper-python

Changing and testing
--------------------
All the `caliper-python` code you would use to build and use a caliper sensor in
your application you can find in the `caliper` main module. The package also
includes a set of test files in a `caliper_tests` main module; we did this not
because we expect you'll use `caliper_tests` in production, but so that the
various tests sub-modules in `caliper_test` can have portable visibility to one
another.

**Testing**. In general, whenever you might make changes to code in `caliper`,
you should run the unit tests. From the package's top-level directory, just use
`tox` to run all the tests on both Python 2.7 and Python 3.4.

**Fixtures**. Your copy of the `caliper-python` repository (or the package) may
include a set of JSON fixtures in `caliper_tests/fixtures` -- these are
the canonical event serializations used to test the sensor against. The
canonical source for these fixtures you can find in the
`Caliper common fixtures repository <https://github.com/IMSGlobal/caliper-common-fixtures>`_.
If there's a local version of these fixtures that came with your
`caliper-python` package or repository, then it's likely the code has been
built to test aginst these fixtures.

Ideally, if you make changes to `caliper-python` you should ensure that your
changes will test well against the fixtures from the main common fixtures
repository. When IMS makes updates to the `caliper-python` repository, it
ensures that the updates do test well against the common fixtures repository.


Using the package
=================
We made `caliper-python` with a lean integration layer for your application. To
use the package, your application needs only::

  import caliper

Your application will need awareness of these parts of the package:

* The `caliper.HttpOptions` class (for use with simple HTTP transport to a Caliper
  endpoint).

* The `caliper.Sensor` class and its API.

* The appropriate `caliper.profiles` metric profile enumeration classes that
  contain the metric profile actions you want to support.

* The `Event` and `Entity` subclasses (found in `caliper.events` and
  `caliper.entities`, respectively) that you will need to use for the metric
  profile actions you want to support.

Here is a very simple example code scrap that demonstrates how an application
might send a basic navigation event to a caliper endpoint::

  import caliper

  the_config = caliper.HttpOptions(
        host='http://caliper-endpoint.your-school.edu/events/',
        auth_scheme='Bearer',
        api_key='your-caliper-API-key' )

  the_sensor = caliper.build_sensor_from_config(
          sensor_id = 'http://learning-app.your-school.edu/sensor',
          config_options = the_config )

  # Here, you will have caliper entity representations of the various
  # learning objects and entities in your wider system, and you provide
  # them into the constructor for the event that has just happened.
  #
  # Note that you don't have to pass an action into the constructor because
  # the NavigationEvent only supports one action, part of the
  # Caliper base profile: caliper.profiles.CaliperProfile.Actions['NAVIGATED_TO']
  #
  the_event = caliper.events.NavigationEvent(
          actor = the_user_currently_acting_as_caliper_Actor_entity,
          edApp = your_application_as_caliper_SoftwareApplication_entity,
          group = the_course_offering_in_play_as_caliper_Organization_entity,
          event_object = the_caliper_DigitalResource_the_actor_is_using,
          navigatedFrom = the_caliper_DigitalResource_the_actor_came_from,
          target = the_caliper_DigitalResource_the_actor_is_going_to,
          endedAtTime = the_time_when_the_actor_did_the_action )

  # Once built, you use your sensor to send your event
  the_sensor.send(the_event)

Your actual use of the caliper code will certainly be more complex than
this. For assistance getting from this very simple example through to more
complex and realistic code-use, we encourage you to look at the unit tests in
the package, and the common fixtures they test against.


Copyright and License
=====================
For details about the copyright and license information, see the NOTICE file.

©2015 IMS Global Learning Consortium, Inc. All Rights Reserved.  
Trademark Information - http://www.imsglobal.org/copyright.html
