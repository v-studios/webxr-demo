========================
 WebXR Hello World Demo
========================

Requirements from Card
======================

WebXR implements a subset of OpenXR, visible to a browser.

Create an app so that Apple Vision Pro and other browser and device users can see it.

This domain ``v-d-d.com`` is in AWS Route53 with NS and SOA records only (main
account, not Organization). See the GDoc on its creation. Create a host record
(A, Alias, etc) to “webxr-demo” under that to something which hosts the app --
an APIGW or Function URL to Lambda, or an S3 bucket configured as a website, or
maybe an external GitHub Pages location.


Background Reading
==================

“XR” is used to show the combination of VR and AR, virtual and augmented reality.

WebXR is a Web application programming interface] that describes support for
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

The A-Frame site recommenends using Glitch but it was shutdown in July. Instead,
use `CodePen <https://codepen.io>`_. This is handy for testing new ideas and can
be shown to others; it supports HTTPS so remote VR viewers can access it.

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

We can host it on GitHub Pages as it supports HTTPS. GH Pages requires the repo
to be made public, which is fine -- no secrets here! I've configured it to use
the ``main`` branch with ``index.html``. See it at::

  https://v-studios.github.io/webxr-demo/

I've not set an AWS Route53 CNAME for it yet. 

Amazon S3 is obvious but it only serves HTTP, and we'd need HTTPS for Android
WebXR to interact with the device sensors. We could add CloudFront to get HTTPS.
But I'd really like to get out of the infrastructure business.

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