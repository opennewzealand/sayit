---
layout: default
title: Developing The Website
---

Developing the SayIt Website
============================

The SayIt website is built on Django 1.4.

Models
------

The models the site uses are:

- Instance - An instance is a collection of speeches with its own subdomain editable by the same users.
- Speaker - A person who gave a speech, basically a cache of a name and api_url from one or more PopIt instances.
- Speech - The main model, audio and/or text, with various metadata about when and where it was given.
- Tag - Speeches can be tagged.
- Recording - Some audio from a mobile app before it's been divided into speeches.
- RecordingTimestamp - A Speaker/Datetime pair that signifies a point in a recording where someone started speaking.
- Section - A hierarchical grouping of speeches.
- LoginToken - providing an easier way for people using the Spoke app to log in

### Custom Managers

There are custom managers defined for Speakers and Speeches which do some of
the weird stuff we have to deal with for our models, like creating a series of
speeches from a recording.

Views
-----

There's nothing special about any of the views, except that the recording API
one is csrf_exempted so that the Spoke app can POST straight to it, and it
specifies that only the http POST method is allowed, so that there's no chance
of confusion with the real forms.

API
---

The recording upload API is implemented as a custom Django model form/view
setup. Other API stuff is currently done with tastypie.

The standard form process is hijacked a bit to make the recording API return
JSON instead of HTML, using a mixin that serialises the errors or resulting
object from a POST request. With the objects this looks a bit hacky, because of
some quirks in how the built-in serializer handles things, but it works.

The APIs try to be relatively RESTful, returning a JSON representation of the
object they created, but with recordings it's not totally possible, because one
request can create multiple speeches.

Transcribing
------------

Transcribing happens in a class called `TranscribeHelper` in `speeches.utils`,
which has the methods needed to call AT&T and return the best transcription it
offers. In theory if we needed to switch to another transcription service, this
could be swapped out with something else, though it might need some
generalisation of the interface.

This class isn't called directly by anything except the `transcribe_speech`
task, which lives in `speeches.tasks` and is run by Celery.

Audio Conversions
-----------------

Audio conversion happens two ways - from whatever is uploaded to a mono .wav
for AT&T, and from whatever is uploaded to .mp3 for long-term storage on the
site. The .wav files are kept in a temp file, for only as long as the
transcription calls take, while the mp3s are obviously stored with the
speeches for as long as they're around.

The code to do this lives a class called `AudioHelper` in `speeches.utils`,
which has `make_wav` and `make_mp3` methods. It also deals with splitting a
recording into multiple mp3. All these functions basically boil down to
`subprocess.call(['ffmpeg', ...])` with various options.

Instances
---------

If you need to add new models/views/forms to the speeches app, you'll probably
need to apply a mixin from the instances app to work appropriately.

- Models: mix in InstanceMixin to anything that's per-instance; if you have a
  custom manager make it a subclass of InstanceManager.
- Views: mix in InstanceViewMixin to any display view (it restricts the default
  queryset to the request's instance), or InstanceFormMixin to any
  create/update view on a per-instance model (it does that, and saves the
  current instance to the model before saving).
- Forms: Just need to exclude instance from any model form that uses
  InstanceMixin.

There's also a for_instance method on any InstanceMixin model to return all
objects for the request's instance. Everything mixed in uses that by default,
like magic.

