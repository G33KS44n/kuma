.. _Troubleshooting:

Troubleshooting
===============

Kuma has many components. Even core developers need reminders of how to keep
them all working together. This doc outlines some problems and potential
solutions running Kuma.


Kuma "Reset" script
-------------------

If you're using the vagrant vm, we have a script that attempts to fix some of
the most common problems with Kuma that are described below.

`kuma-vagrant-reset.sh <https://gist.github.com/openjck/b69445fa3e34e1780377>`_


.. _Running individual processes:

Running individual processes
----------------------------

It is usually easier to see and debug problems if you run MDN processes
individually, instead of running them via ``foreman``. You can run each process
exactly as it is listed in ``Procfile``

-  ``runserver`` - runs the Django development server

-  ``celery worker`` - runs the celery worker process for tasks

-  ``celerycam`` - stores a snapshot of celery tasks to display in admin site

-  ``kumascript`` - runs the node.js process for KumaScript macros

-  ``stylus`` - runs a process to compile all ``.styl`` changes into ``.css``


Errors after switching branches
-------------------------------

-  If you are using a vm, you should occasionally re-run the Puppet setup,
   especially after updating code with major changes. This will ensure that the
   VM environment stays up to date with configuration changes and installation
   of additional services.

   -  On the Host::

          vagrant provision

   -  Inside the VM::

          sudo puppet apply /home/vagrant/src/puppet/manifests/dev-vagrant.pp

-  If you see ``ImportError:`` errors, you may need to update your git
   submodules and/or clean out your ``*.pyc`` files to make sure python has all
   the latest files it needs.::

       git submodule update --init
       find . -name "*.pyc" | xargs rm -f

-  If you see ``DatabaseError: (1146, "Table '...' doesn't exist")`` errors,
   you probably need to run database migrations.::

       python manage.py migrate

   Note: If you are using a vm, this is done when you re-run the Puppet setup.


Errors with KumaScript
----------------------

KumaScript is a very intensive process. If you are only working on python code
or front-end code that doesn't affect live site content, you can usually avoid
running it. (See `Running individual processes`_.)

-  If you see lots of KumaScript timeout errors and you're running a vm, try
   increasing the memory allocated to the vm. If you're using the vagrant vm::

       memory_size: 4096

   in the ``vagrantconfig.yaml`` file.

-  If you see ``Kumascript service failed unexpectedly: HTTPConnectionPool``,
   make sure you enabled :ref:`KumaScript <enable KumaScript>`.


Errors with styles
------------------

-  If you don't see your ``styl`` changes on the site, make sure you've
   compiled the ``.styl`` files into ``.css``, either manually::

       ./scripts/compile-stylesheets

   Or with the automatic watch process::

       ./scripts/compile-stylesheets --watch

-  Some MDN features (e.g., prism or ckeditor) make explicit requests for
   compressed assets. If you notice styles are broken and the page is getting
   404s on ``.css``, generate the compressed assets for these features::

       python manage.py compress_assets


.. _more-help:

Getting more help
-----------------

If you have more problems running Kuma, please:

#. Paste errors to `pastebin`_
#. email the `dev-mdn`_ list
#. After you email dev-mdn, you can also ask in `IRC`_

.. _pastebin: http://pastebin.mozilla.org/
.. _dev-mdn: mailto:dev-mdn@lists.mozilla.org?subject=vagrant%20issue
.. _IRC: irc://irc.mozilla.org:6697/#mdndev
.. _puppet: http://puppetlabs.com/puppet/puppet-open-source
