# Citrix Cloud API documentation #

Documentation for the APIs available from Citrix Cloud and the services it offers.

The documentation format is reStructuredText (rst).  http://docutils.sourceforge.net/rst.html

Code highlighting is done with the pygments library, so should support all programming
languages listed on http://pygments.org/languages/.  Check docs/source files for
examples of syntax highlighted code fragments.

To build, you must have the following installed:

* Python, https://www.python.org.  Tested with version 3.5.2.
* Sphinx, http://www.sphinx-doc.org.  Tested with version 1.5.1.
* The Sphinx "ReadTheDocs" (rtd) theme.  Tested with version 0.1.9.
* Sphinxcontrib-Disqus, https://robpol86.github.io/sphinxcontrib-disqus/index.html.  Tested with version 1.1.0.

  Easy installation method: pip install sphinx sphinx_rtd_theme sphinxcontrib-disqus

Then run 'make html' (or 'make.bat html' on Windows).  The output is in build/html.

Note that the Disqus integration only works when the site is accessed via an
'http://' or 'https://' scheme.  If you load the site locally ('file://' scheme)
the comment sections will not appear.  If you want to debug the site by running
it locally, and you need to see Disqus working, use:

  python -m SimpleHTTPServer 8080

Then load http://localhost:8080 in your browser.
