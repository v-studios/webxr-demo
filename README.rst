========================
 WebXR Hello World Demo
========================

TL;DR: I used A-Frame to create a scene with some objects, two of which can be
interacted with. It's hosted on GitHub Pages with custom domain
https://webxr-demo.v-v-d.com. 


Requirements from Card
======================

WebXR implements a subset of OpenXR, visible to a browser.

Create an app so that Apple Vision Pro and other browser and device users can
see it.

This domain ``v-d-d.com`` is in AWS Route53 with NS and SOA records only (main
account, not Organization). See the GDoc on its creation. Create a host record
(A, Alias, etc) to “webxr-demo” under that to something which hosts the app --
an APIGW or Function URL to Lambda, or an S3 bucket configured as a website, or
maybe an external GitHub Pages location.


Background Reading
==================

“XR” is used to indicate the combination of VR and AR, virtual and augmented
reality.

WebXR is a Web application programming interface that describes support for
accessing augmented reality and virtual reality devices, such as the HTC Vive,
Oculus Rift, Meta Quest, Google Cardboard, HoloLens, Apple Vision Pro, Android
XR-based devices, Magic Leap or Open Source Virtual Reality (OSVR), in a web
browser.

* `WebXR Demo <https://modelviewer.dev/examples/augmentedreality/>`_ with chair,
  canoe, ...
* `Mozilla Development Network
  <https://developer.mozilla.org/en-US/docs/Games/Techniques/3D_on_the_web/Building_up_a_basic_demo_with_A-Frame>`_
* `4-hour livestream documentation
  <https://medium.com/samsung-internet-dev/making-an-ar-game-with-aframe-529e03ae90cb>`_
  mentioning making the sky transparent for AR devices 
* `Demo adds physics and hand grabbers
  <https://medium.com/samsung-internet-dev/simple-and-quick-physics-in-webxr-using-a-frame-6ed82dc0590e>`_
  to stack cubes
* An `interactive game
  <https://medium.com/@mattnutsch/tutorial-how-to-make-webxr-games-with-a-frame-eedd98613a88>`_
  is more involved
* `A-Frame's own documentation
  <https://aframe.io/docs/1.7.0/introduction/vr-headsets-and-webxr-browsers.html>`_
* `A-Frame Examples <https://stemkoski.github.io/A-Frame-Examples/>`_ with textures and other
  features
* `Gentle introduction to A-Frame <https://codehs.com/documentation/aframe>`_
  as part of a larger document-everything site

Choosing A-Frame
----------------

`A-Frame <https://aframe.io/>`_ is higher level than raw JS or ``three.js`` and
this should make it faster to develop with than those or gaming platforms like
Unity or Unreal Engine.

A-Frame is a web framework for building virtual reality experiences. It is built
on top of HTML and provides an easy way to create 3D scenes that can be viewed
in VR devices. A-Frame is an open-source project maintained by Mozilla and has a
large community of contributors. A-Frame uses a declarative syntax, which means
that you can create 3D scenes using HTML-like tags. This makes it easy to get
started with VR development, even if you are not familiar with JavaScript or 3D
graphics programming. A-Frame is designed to be compatible with a wide range of
VR devices, including the HTC Vive, Oculus Rift, Meta Quest, Google Cardboard,
HoloLens, Apple Vision Pro, Android XR-based devices, Magic Leap, and Open
Source Virtual Reality (OSVR).   

A-Frame also supports a wide range of web browsers, including Chrome, Firefox,
Safari, and Edge. This means that you can create VR experiences that can be
viewed on a wide range of devices, including desktop computers, laptops,
tablets, and smartphones.


Demoing
=======

Developing, Testing Locally
---------------------------

The A-Frame site recommends using Glitch but it was shutdown in July. Instead,
use `CodePen <https://codepen.io>`_. This is handy for testing new ideas and can
be shown to others; it supports HTTPS so remote VR viewers can access it.
JSFiddle can also do this, see `this example
<https://jsfiddle.net/pbl808HS/pLkbkv8b/>`_.

You can run a local python HTTPserver so you can see edits to your code files
quickly. Serve the ``index.html`` locally with HTTP on port 9000::

  python -m http.server 9000

Demoing remotely
----------------

Ngrok used to be good but it's gotten bloated and requires signup and
authentication token; not hard, just annoying.

Service ``localhost.run`` is trivial to use with a simple ssh tunnel command. If
our local server is running on port 9000, we can run::

  ssh -R 80:localhost:9000 localhost.run

and it will give us an HTTPS URL as well as a QR code we can scan with a phone.

If you upload your SSH public key, you can auth with user ``plan`` which gives
you a longer duration. For custom domains, you need to subscribe.

  ssh -R 80:localhost:9000 plan@localhost.run

Hosting
-------

Amazon S3 is obvious but it only serves HTTP, and we'd need HTTPS for Android
WebXR to interact with the device sensors. We could add CloudFront to get HTTPS.
But I'd really like to get out of the infrastructure business.

We can host it on GitHub Pages as it supports HTTPS. GH Pages requires the repo
to be made public, which is fine -- no secrets here! I've configured it to use
the ``main`` branch with ``index.html``. See it at::

  https://v-studios.github.io/webxr-demo/

To make the DNS name work, I *manually* created a ``CNAME`` record in AWS
Route53::

  webxr-demo.v-v-d.com CNAME v-studios.github.io

In GitHub Pages (https://github.com/v-studios/webxr-demo/settings/pages), I set
the Custom domain to be webxr-demo.v-v-d.com. After hitting Save, GH verified
the DNS name and made it live. Now we can point our browser to::

  https://webxr-demo.v-v-d.com/

Going to the original ``github.io`` URL now redirects to this custom domain.


Interaction
===========

I want to be able to interact with objects, even on a web page. This `simple
demo with 4 objects is fine <https://codepen.io/Absulit/pen/WEKjqm>`_, but its
a-frame version is 0.6.1 (see HTML Settings Header) -- 8 years. If I change it
to the current 1.7.1, it breaks the demo. The documentation on `Animating on
Events
<https://aframe.io/docs/1.7.0/guides/building-a-basic-scene.html#animating-on-events>`_
ws helpful. I had to do some looking around to see how to invoke the "fuse"
(stare a while) and also the browser's mouse click event. 

I've created a scene with two cubes, two cylinders, our orange V! Studios
cone-and-sphere demo, and also added a `TMA-1 monolith
<https://en.wikipedia.org/wiki/Monolith_(Space_Odyssey)>`_. If you rotate to
point the circular gaze target on the sphere or TMA-1 it will cause them to
change; try it! In a browser, you can also click with the mouse. 

A web browser can move in XYZ using cursor or WASD keys. A cell phone doesn't
have these keys, so it can only do rotation. It would be nice to add a virtual
WASD controller to the scene.

I used to be able to rotate the phone and see the view change, but now it
doesn't do that on my Android with Brave -- drag on the screen to change the
view. I think this has to do with adding the `mouse-cursor` or `rayCaster` but I
don't know. I need to read more and experiment to understand the interactions of
virtual cursors. 

On my phone, when I first enter the site, there's a "VR" button in the lower
right corner that allows me to enter VR mode. When I hit it, I get a split
screen, and rotation does rotate around the scene; I should get a `Google
Cardboard <https://www.amazon.com/s?k=google+cardboard>`_ or other VR phone
frame to play with this.

Sound -- not working
====================

I tried to follow the docs on `Adding Audio
<https://aframe.io/docs/1.7.0/guides/building-a-basic-scene.html#adding-audio>`_.
I wanted the TMA-1 monolith to emit a sound that got louder as you got closer.
Unfortunately, it didn't work, and this appears to be because browsers block
"autoplay" in the interest of user experience::

  The AudioContext was not allowed to start. It must be resumed (or created)
  after a user gesture on the page. 
  https://developer.chrome.com/blog/autoplay/#web_audio

I don't have a work-around yet. 

TODO
====

* Figure out how to make rotation work with phone just by moving it
* Add WASD controller for movement on phone
* Get sound working
