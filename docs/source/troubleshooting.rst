=================
 Troubleshooting
=================

Issue: Conda claims that a package is installed, but it appears not to be.
==========================================================================

Sometimes conda will claim that a package is already installed, but it will
not appear to be, e.g., a Python package that gives ImportError.

There are a few possible causes of this issue:

Make sure you are in the right conda environment.
-------------------------------------------------

``conda info`` will tell you what environment is currently active (under
"default environment"). You can verify that you are using the Python from the
correct environment by running

.. code:: python

   import sys
   print(sys.prefix)

For Python packages, make sure you do not have ``PYTHONPATH`` or ``PYTHONHOME`` set.
------------------------------------------------------------------------------------

The command ``conda info -a`` will show you the values of these environment
variables.

These environment variables cause Python to load files from locations other
than the standard ones. Conda works best when these environment variables are
not set, as their typical use-cases are obviated by Conda environments, and a
common issue is that they will cause Python to pick up the wrong versions or
broken versions of a library.

To unset them temporarily for the current terminal session, run ``unset
PYTHONPATH``. To unset them permanently, check for lines in the files
``~/.bashrc``, ``~/.bash_profile``, ``~/.profile`` if you use bash,
``~/.zshrc`` if you use zsh, or the file output by ``$PROFILE`` if you use
PowerShell on Windows.

For Python packages, remove any site-specific directories.
----------------------------------------------------------

Another possibility for Python are so-called site-specific files. These
typically live in ``~/.local`` on Unix. The full description of where
site-specific packages can be found is in `PEP 370
<http://legacy.python.org/dev/peps/pep-0370/>`_. As with ``PYTHONPATH``,
Python may try importing packages from this directory, which can cause
issues. The recommended fix is to remove the site-specific directory.

For C libraries, unset the environment variables ``LD_LIBRARY_PATH`` on Linux and ``DYLD_LIBRARY_PATH`` on Mac OS X.
--------------------------------------------------------------------------------------------------------------------

These act similarly to ``PYTHONPATH`` for Python. If they are set, they can
cause libraries to be loaded from locations other than the Conda
environment. Again, Conda environments obviate most use-cases for these
variables, so it is recommended to unset them if they are set, unless you know
what you are doing. ``conda info -a`` will show what these are set to (on the
relevant operating system).

Occasionally, an installed package will become corrupted.
---------------------------------------------------------

Conda works by unpacking the packages in the pkgs directory and then hard
linking them to the environment. Sometimes these get corrupted somehow,
breaking all environments that use them, and also any additional environments,
since the same files are hard linked each time.

**conda install -f will unarchive the package again and re-link it.** It also
does a md5 verification on the package (usually if this is different, it's
because your channels have changed and there is a different package with the
same name, version, and build number). Note that this breaks the links to any
other environments that already had this package installed, so you'll have to
reinstall it there too. It also means that running ``conda install -f`` a lot
can use up a lot of disk space if you have a lot of environments.  Note that
the ``-f`` flag to ``conda install`` (``--force``) implies ``--no-deps``, so
``conda install -f package`` will not reinstall any of the dependencies of
``package``.
