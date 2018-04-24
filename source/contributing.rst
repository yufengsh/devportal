=================================================
Contributing to the Citrix Cloud developer portal
=================================================

The source to this portal is hosted at https://github.com/CitrixCloudCommunity/devportal.


Getting set up
~~~~~~~~~~~~~~

#. Create a github account if you don't already have one.

#. Create a fork of the developer portal project located at https://github.com/CitrixCloudCommunity/devportal.

#. Clone your fork to a machine where you can make edits.

::

  git clone https://github.com/<your fork repository>

4. Install Python (https://www.python.org).  Tested with version 3.5.2.

    * On Windows, you might need to add python to your path manually.

5. Make sure ``pip`` is up-to-date.

::

  pip install --upgrade pip
  pip install --upgrade setuptools

6. Install other prerequisites using ``pip``.

::

  pip install sphinx sphinx_rtd_theme sphinxcontrib-disqus

7. Build the html locally.

    * On Windows, ``make.bat html``
    * On other systems, ``make html``


Making the changes you want
~~~~~~~~~~~~~~~~~~~~~~~~~~~

The documentation format is "reStructured Text" or RST.  You can find this documented in detail at: http://docutils.sourceforge.net/rst.html.  This format is like markdown but with more functionality.

Start every file with a header containing the service name, and end it with a "Comments" section linking to disqus so that your page will include the discussion forum functionality.  Every page you create should follow the same initial template:

.. code-block:: rst

    ===========
    Widget APIs
    ===========

    *Fill in the rest of the content here*

    
    Comments
    ========

    .. disqus::


Iterate on your content until you are happy with it.  Test your code by running ``make linkcheck`` before submitting.  As a final check, ``make clean`` and then ``make html`` and ensure there are NO warnings.


Submitting your changes back to Citrix Cloud
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

When you are ready to release your new documentation onto the world, commit your changes and push them back to your fork on github:

::

  git add .
  git commit -m "My changes are the best"
  git push

Now open a pull request from your github fork, back to the CitrixCloudCommunity/devportal project.


Comments
========

.. disqus::

