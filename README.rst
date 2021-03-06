dgroc-kf5
=========

Fork of dgroc script written by Pierre-Yves Chibon (https://github.com/pypingou/dgroc)
for automated git rebuilds on Copr.

This fork is specially tailored for automated builds of KDE Frameworks 5 for Fedora
in Copr (https://github.com/FedoraKDE/fedora-kde-frameworks)

:Author: Daniel Vrátil <dvratil@redhat.com>
:Original Author: Pierre-Yves Chibon <pingou@pingoured.fr>

dgroc: Daily Git Rebuild On Copr

This project aims at easily provide daily build of a project tracked via
git and made available via `copr <http://copr.fedoraproject.org>`_.



This is 


Get it running
==============

* Retrieve the sources::

    git clone git//github.com:pypingou/dgroc.git


* Create the configuration file ``~/.config/dgroc``

* Fill the configuration file, for example::

    [main]
    username = me
    email = my_email@example.com
    copr_url = https://copr.fedoraproject.org/
    copr_name = subsurface
    upload_command = cp %s /var/www/html/subsurface/
    upload_url = http://my_server/subsurface/%s
    #no_ssl_check = True  # No longer required now that copr has a valid cert

    [subsurface]
    git_url = git://subsurface.hohndel.org/subsurface.git
    git_folder = /tmp/subsurface/
    git_branch = v4.0-branch
    spec_file = ~/GIT/subsurface/subsurface.spec

    [guake]
    git_url = https://github.com/Guake/guake.git
    git_folder = /tmp/guake/
    spec_file = ~/GIT/guake/guake.spec
    patch_files = ~/GIT/guake/guake-0.2.2-fix_vte.patch,
                  ~/GIT/guake/0001-Remove-vte-check-in-the-configure.ac.patch


The Main section
----------------
``username`` User name in `copr`_

``realname`` Real name to use in the changelog of the spec file when updating
it. When not specified, ``username`` will be used.

``email`` The email to use in the changelog of the spec file when updating
it.

``copr_url`` The url of `copr`_ server to use.

``copr_name`` Name of the `copr`_ to use.

``upload_command`` The command to run to make the source rpm (src.rpm, srpm)
available to copr. This can be a copy command (cp) or a copy over ssh (scp).
Note that the ``%s`` is important, it will be replaced by the full path to
the source rpm created.

`upload_url` The url of the source rpm once it has been uploaded. Note that
here as well the ``%s`` is important as it will be replaced by the source
rpm file name.

For example, if you upload your source rpm onto your fedorapeople space, your
``upload_command`` might be: ``scp %s fedorapeople:public_html/srpms/`` and
your ``upload_url`` might be: ``https://pingou.fedorapeople.org/srpms/%s``.

``no_ssl_check`` Simple boolean to check the ssl certificate when starting
the build on copr. At the moment the ssl certificate is self-signed and thus
invalid. So using the ``https`` version of ``copr_url`` will require a
``no_ssl_check`` set to ``True``.


The project section
-------------------

For each project, only three options are required:

``git_url`` The url to the git repository, that is only required if the git
repo is not already cloned on the disk (see ``git_folder``).

``git_folder`` The location of the local clone of the git repository to
build.

``git_branch`` A branch to checkout out.

``spec_file`` The location of the spec file for the project to build.

``patch_files`` A comma separated list of patches required to build the
project.
These files will be copied over to the rpm sourcedir to be present when
building the source rpm.

.. Note:: The spec file should be fully functionnal as all ``dgroc`` will do is
          update the ``Source0``, ``Release`` and add an entry in the ``Changelog``.

.. Note:: You might have to set in your spec file the %setup line to::

              %setup -q -n <projectname>


Run the project
---------------

From the sources, it requires few steps::

* Install dependencies::

  yum install libgit2-devel python-virtualenvwrapper

* Create a virtual env::

  mkvirtualenv dgroc

* Install the python dependencies::

  pip install -r requirements.txt

* Run dgroc::

  ./dgroc.py

For more information/output run ``./dgroc.py --debug``
