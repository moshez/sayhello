Saying Hello
============

Introduction
------------

WSGI_ has been a successful standard.
Very successful.
It allows people to write Python applications
using many frameworks
(Django_, Pyramid_, Flask_ and Bottle_, to name but a few)
and deploy using many different servers
(uwsgi_, gunicorn_ and Apache_).

Twisted_ makes a good WSGI container.
Like Gunicorn, it is pure Python, simplifying deployment.
Like Apache, it sports a production-grade web server that
does not need a front end.

Modern web applications tend to be complex beasts.
In order to be trusted by users,
they need to have TLS support, signed by a trusted CA.
They also need to transmit a lot of static resources --
images, CSS and JavaScript files,
even if all HTML is dynamically generated.
Deploying them often requires complicated set-ups.

.. _WSGI: https://wsgi.readthedocs.io/en/latest/
.. _Django: https://www.djangoproject.com/
.. _Pyramid: http://docs.pylonsproject.org/en/latest/docs/pyramid.html#pyramid-documentation
.. _Flask: http://flask.pocoo.org/
.. _Bottle: https://bottlepy.org/docs/dev/
.. _Twisted: https://twistedmatrix.com/documents/16.5.0/web/howto/web-in-60/wsgi.html
.. _uwsgi: https://uwsgi-docs.readthedocs.io/en/latest/
.. _Apache: https://modwsgi.readthedocs.io/en/develop/
.. _Gunicorn: http://gunicorn.org/

Containers
----------

`Container images`_ allow us to package an application
with all of its dependencies.
They often cause a temptation to use those as the configuration management.
However, Dockerfile_ is a challenging language to write big parts of
the application in.
People writing WSGI applications probably think Python is a good
programming language.
The more of the application logic is in Python,
the easier it is for a WSGI-based team to master it.

.. _Container images: https://glyph.twistedmatrix.com/2016/10/what-am-container.html
.. _Dockerfile: https://docs.docker.com/engine/userguide/eng-image/dockerfile_best-practices/#/add-or-copy

PEX
---

Pex_ is a way to package several Python "distributions"
(sometimes informally called "Packages",
the things that are hosted by PyPI_)
into one file,
optionally with an entry-point so that running the file
will call a pre-defined function.
It can take an explicit list of wheels but can also,
as in our example here,
take arguments compatible with the ones pip_ takes.
The best practice is to give it a list of wheels,
and build the wheels with :code:`pip wheel`.

.. _Pex: https://pex.readthedocs.io/en/stable/
.. _pip: https://pip.pypa.io/en/stable/
.. _PyPI: https://pypi.python.org/pypi

pkg_resources
-------------

The pkg_resources_ module allows access to files packaged in a distribution
in a way that is agnostic to how the distribution was deployed.
Specifically, it is possible to install a distribution
as a `zipped directory`_,
instead of unpacking it into :code:`site-packages`.
The code:`pex` format relies on this feature of Python,
so adherence to using :code:`pkg_resources` to access `data files`_
is important in order to not break code:`pex` compatibility.

.. _pkg_resources: https://setuptools.readthedocs.io/en/latest/pkg_resources.html
.. _zipped directory: https://docs.python.org/2/library/zipimport.html
.. _data files: https://docs.python.org/2/distutils/setupscript.html#installing-package-data

Let's Encrypt
-------------

`Let's Encrypt`_ is a free, automated, and open Certificate Authority. 
It has invented the ACME_ protocol in order to make
getting secure certificates a simple operation.
txacme_ is an implementation of an ACME client,
i.e., something that asks for certificates,
for Twisted applications.
It uses the `server endpoint`_ plugin mechanism
in order to allow any application that builds a listening endpoint
to support ACME.

.. _Let's Encrypt: https://letsencrypt.org/donate/
.. _ACME: https://github.com/letsencrypt/acme-spec
.. _txacme: https://txacme.readthedocs.io/en/latest/
.. _server endpoint: https://twistedmatrix.com/documents/16.5.0/api/twisted.internet.interfaces.IStreamServerEndpointStringParser.html

Twist 
-----

The :code:`twist` command-line tools allows running
any Twisted `service plugin`_.
Service plugins allow us to configure a service using Python,
a pretty nifty language,
while still allowing specific customizations at the point of use
via command line parameters.

.. _service plugin: https://twistedmatrix.com/documents/current/core/howto/tap.html

Putting it all together
-----------------------

Our :code:`setup.py` files defines a distribution called :code:`sayhello`.
In it, we have three parts:

* :code:`src/sayhello/wsgi.py`: A simple Flask-based WSGI application
* :code:`src/sayhello/data/index.html`: an HTML file meant to serve as the root
* :code:`src/twisted/plugins/sayhello.py`: A Twist plugin

Credits
-------

Glyph Lefkowitz has inspired me in his blog_ about how to build efficient containers. He has also spoken_ about how deploying applications should be no more than one file copy.

Tristan Seligmann has written txacme.

Amber "Hawkowl" Brown has written "twist",
which much better at running Twisted-based services than
the older "twistd".

.. _blog: https://glyph.twistedmatrix.com/2015/03/docker-deploy-double-dutch.html
.. _spoken: http://pyvideo.org/djangocon-2011/djangocon-2011--keynote---glyph-lefkowitz.html
