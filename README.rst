
develbase
*********

.. image:: https://gitlab.com/constrict0r/develbase/badges/master/pipeline.svg
   :alt: pipeline

.. image:: https://travis-ci.com/constrict0r/develbase.svg
   :alt: travis

.. image:: https://readthedocs.org/projects/develbase/badge
   :alt: readthedocs

Ansible role to apply basic developer configuration.

.. image:: https://gitlab.com/constrict0r/img/raw/master/develbase/develbase.png
   :alt: develbase

Full documentation on `Readthedocs
<https://develbase.readthedocs.io>`_.

Source code on:

`Github <https://github.com/constrict0r/develbase>`_.

`Gitlab <https://gitlab.com/constrict0r/develbase>`_.

`Part of: <https://gitlab.com/explore/projects?tag=doombots>`_

.. image:: https://gitlab.com/constrict0r/img/raw/master/develbase/doombots.png
   :alt: doombots

**Ingredients**

.. image:: https://gitlab.com/constrict0r/img/raw/master/develbase/ingredients.png
   :alt: ingredients


Contents
********

* `Description <#Description>`_
* `Usage <#Usage>`_
* `Variables <#Variables>`_
   * `expand <#expand>`_
   * `group <#group>`_
   * `packages <#packages>`_
   * `packages_js <#packages-js>`_
   * `packages_pip <#packages-pip>`_
   * `packages_purge <#packages-purge>`_
   * `password <#password>`_
   * `repositories <#repositories>`_
   * `services <#services>`_
   * `services_disable <#services-disable>`_
   * `system_skeleton <#system-skeleton>`_
   * `upgrade <#upgrade>`_
   * `users <#users>`_
   * `user_skeleton <#user-skeleton>`_
   * `user_tasks <#user-tasks>`_
   * `configuration <#configuration>`_
* `YAML <#YAML>`_
* `Attributes <#Attributes>`_
   * `item_name <#item-name>`_
   * `item_pass <#item-pass>`_
   * `item_groups <#item-groups>`_
   * `item_expand <#item-expand>`_
   * `item_path <#item-path>`_
* `Requirements <#Requirements>`_
* `Compatibility <#Compatibility>`_
* `License <#License>`_
* `Links <#Links>`_
* `UML <#UML>`_
   * `Deployment <#deployment>`_
* `Author <#Author>`_

Description
***********

Ansible role to apply basic developer configuration.

This is capable of:

* Upgrade the system.

* Add `apt <https://wiki.debian.org/Apt>`_ repository sources.

* Update the apt cache.

* Uninstall apt packages.

* Install apt packages.

* Install `yarn <https://yarnpkg.com>`_ packages.

* Install `pip <https://pypi.org/project/pip/>`_ packages.

* Apply system-wide configuration using git.

* Stop services and disable them.

* Enable services and restart them.

* Create users.

* Add users to groups.

* Apply user-wide configuration using git.

* Run custom user tasks.

By default this role applies the following configuration:

* Installs the base software:

..

   * apt-transport-https

   * bzip2

   * ca-certificates

   * curl

   * sudo

   * unrar-free

   * unzip

   * vim

   * wget

   * xz-utils

* Installs the base developer software:

..

   * bats

   * bchunks

   * emacs

   * flac

   * git

   * libtext-csv-perl

   * make

   * meld

   * retext

   * ssh-askpass

   * texlive-bibtex-extra

   * texlive-latex-base

   * texlive-latex-extra

   * tree

* Configures the base software:

..

   * vim

   ..

      * Creates a *.vimrc* configuration file on each user home
         directory.

      * Enable syntax highlight.

      * Set two spaces instead of tabs.


Usage
*****

* To install and execute:

..

   ::

      ansible-galaxy install constrict0r.develbase
      ansible localhost -m include_role -a name=constrict0r.develbase -K

* Passing variables:

..

   ::

      ansible localhost -m include_role -a name=constrict0r.develbase -K \
          -e "{packages: [leafpad, rolldice]}"

* To include the role on a playbook:

..

   ::

      - hosts: servers
        roles:
            - {role: constrict0r.develbase}

* To include the role as dependency on another role:

..

   ::

      dependencies:
        - role: constrict0r.develbase
          packages: [leafpad, rolldice]

* To use the role from tasks:

..

   ::

      - name: Execute role task.
        import_role:
          name: constrict0r.develbase
        vars:
          packages: [leafpad, rolldice]

* To run tests:

..

   ::

      cd develbase
      chmod +x testme.sh
      ./testme.sh

   On some tests you may need to use *sudo* to succeed.


Variables
*********

The following variables are supported:


expand
======

Boolean value indicating if load items from file paths or URLs or just
treat files and URLs as plain text.

If set to *true* this role will attempt to load items from the
especified paths and URLs.

If set to *false* each file path or URL found on packages will be
treated as plain text.

This variable is set to *false* by default.

::

   ansible localhost -m include_role -a name=constrict0r.develbase \
       -e "expand=true configuration='/home/username/my-config.yml' titles='packages'"

If you wish to override the value of this variable, specify an
*item_path* and an *item_expand* attributes when passing the item, the
*item_path* attribute can be used with URLs too:

::

   ansible localhost -m include_role -a name=constrict0r.develbase \
       -e "{expand: false,
           packages: [ \
               item_path: '/home/username/my-config.yml', \
               item_expand: false \
           ], titles: 'packages'}"

To prevent any unexpected behaviour, it is recommended to always
specify this variable when calling this role.


group
=====

List of groups to add all users into. Each non-empty username will be
added to the groups specified on this variable.

This list can be modified by passing an *groups* array when including
the role on a playbook or via *–extra-vars* from a terminal.

This variable is empty by default.

::

   # Including from terminal.
   ansible localhost -m include_role -a name=constrict0r.develbase -K -e \
       "{group: [disk, sudo]}"

   # Including on a playbook.
   - hosts: servers
     roles:
       - role: constrict0r.develbase
         group:
           - disk
           - sudo

   # To a playbook from terminal.
   ansible-playbook -i tests/inventory tests/test-playbook.yml -K -e \
       "{group: [disk, sudo]}"


packages
========

List of packages to install via apt.

This list can be modified by passing a *packages* array when including
the role on a playbook or via *–extra-vars* from a terminal.

This variable is empty by default.

::

   # Including from terminal.
   ansible localhost -m include_role -a name=constrict0r.develbase -K -e \
       "{packages: [gedit, rolldice]}"

   # Including on a playbook.
   - hosts: servers
     roles:
       - role: constrict0r.develbase
         packages:
           - gedit
           - rolldice

   # To a playbook from terminal.
   ansible-playbook -i tests/inventory tests/test-playbook.yml -K -e \
       "{packages: [gedit, rolldice]}"


packages_js
===========

List of packages to install via yarn.

This list can be modified by passing a *packages_js* array when
including the role on a playbook or via *–extra-vars* from a terminal.

If you want to install a specific package version, then specify *name*
and *version* attributes for the package.

This variable is empty by default.

::

   # Including from terminal.
   ansible localhost -m include_role -a name=constrict0r.develbase -K -e \
       "{packages_js: [node-red, {name: requests, version: 2.22.0}]}"

   # Including on a playbook.
   - hosts: servers
     roles:
       - role: constrict0r.develbase
         packages_js:
           - node-red
           - name: requests
             version: 2.22.0

   # To a playbook from terminal.
   ansible-playbook -i tests/inventory tests/test-playbook.yml -K -e \
       "{packages_js: [node-red, {name: requests, version: 2.22.0}]}"


packages_pip
============

List of packages to install via pip.

This list can be modified by passing a *packages_pip* array when
including the role on a playbook or via *–extra-vars* from a terminal.

If you want to install a specific package version, append the version
to the package name.

This variable is empty by default.

::

   # Including from terminal.
   ansible localhost -m include_role -a name=constrict0r.develbase -K -e \
       "{packages_pip: ['bottle==0.12.17', 'whisper']}"

   # Including on a playbook.
   - hosts: servers
     roles:
       - role: constrict0r.develbase
         packages_pip:
           - bottle==0.12.17
           - whisper

   # To a playbook from terminal.
   ansible-playbook -i tests/inventory tests/test-playbook.yml -K -e \
       "{packages_pip: ['bottle==0.12.17', 'whisper']}"


packages_purge
==============

List of packages to purge using apt.

This list can be modified by passing a *packages_purge* array when
including the role on a playbook or via *–extra-vars* from a terminal.

This variable is empty by default.

::

   # Including from terminal.
   ansible localhost -m include_role -a name=constrict0r.develbase -K -e \
       "{packages_purge: [gedit, rolldice]}"

   # Including on a playbook.
   - hosts: servers
     roles:
       - role: constrict0r.develbase
         packages_purge:
           - gedit
           - rolldice

   # To a playbook from terminal.
   ansible-playbook -i tests/inventory tests/test-playbook.yml -K -e \
       "{packages_purge: [gedit, rolldice]}"


password
========

If an user do not specifies the *password* attribute, this password
will be setted for that user.

This password will only be setted for new users and do not affects
existent users.

This variable defaults to 1234.

::

   # Including from terminal.
   ansible localhost -m include_role -a name=constrict0r.develbase -K -e \
       "{password: 4321}"

   # Including on a playbook.
   - hosts: servers
     roles:
       - role: constrict0r.develbase
         password: 4321

   # To a playbook from terminal.
   ansible-playbook -i tests/inventory tests/test-playbook.yml -K -e \
       "password=4321"


repositories
============

List of repositories to add to the apt sources.

This list can be modified by passing a *repositories* array when
including the role on a playbook or via *–extra-vars* from a terminal.

This variable is empty by default.

::

   # Including from terminal.
   ansible localhost -m include_role -a name=constrict0r.develbase -K -e \
       "{repositories: [{ \
            name: multimedia, \
            repo: 'deb http://www.debian-multimedia.org sid main' \
        }]}}"

   # Including on a playbook.
   - hosts: servers
     roles:
       - role: constrict0r.develbase
         repositories:
           - name: multimedia
             repo: deb http://www.debian-multimedia.org sid main

   # To a playbook from terminal.
   ansible-playbook -i tests/inventory tests/test-playbook.yml -K -e \
       "{repositories: [{ \
            name: multimedia, \
            repo: 'deb http://www.debian-multimedia.org sid main' \
        }]}}"


services
========

List of services to enable and start.

This list can be modified by passing a *services* array when including
the role on a playbook or via *–extra-vars* from a terminal.

This variable is empty by default.

::

   # Including from terminal.
   ansible localhost -m include_role -a name=constrict0r.develbase -K -e \
       "{services: [mosquitto, nginx]}"

   # Including on a playbook.
   - hosts: servers
     roles:
       - role: constrict0r.develbase
         services:
           - mosquitto
           - nginx

   # To a playbook from terminal.
   ansible-playbook -i tests/inventory tests/test-playbook.yml -K -e \
       "{services: [mosquitto, nginx]}"


services_disable
================

List of services to stop and disable.

This list can be modified by passing a *services_disable* array when
including the role on a playbook or via *–extra-vars* from a terminal.

This variable is empty by default.

::

   # Including from terminal.
   ansible localhost -m include_role -a name=constrict0r.develbase -K -e \
       "{services_disable: [mosquitto, nginx]}"

   # Including on a playbook.
   - hosts: servers
     roles:
       - role: constrict0r.develbase
         services_disable:
           - mosquitto
           - nginx

   # To a playbook from terminal.
   ansible-playbook -i tests/inventory tests/test-playbook.yml -K -e \
       "{services_disable: [mosquitto, nginx]}"


system_skeleton
===============

URL or list of URLs pointing to git skeleton repositories containing
layouts of directories and configuration files.

Each URL on system_skeleton will be checked to see if it points to a
valid git repository, and if it does, the git repository is cloned.

The contents of each cloned repository will then be copied to the root
of the filesystem as a simple method to apply system-wide
configuration.

This variable is empty by default.

::

   # Including from terminal.
   ansible localhost -m include_role -a name=constrict0r.develbase -K -e \
       "{system_skeleton: [https://gitlab.com/huertico/server]}"

   # Including on a playbook.
   - hosts: servers
     roles:
       - role: constrict0r.develbase
         system_skeleton:
           - https://gitlab.com/huertico/server
           - https://gitlab.com/huertico/client

   # To a playbook from terminal.
   ansible-playbook -i tests/inventory tests/test-playbook.yml -K -e \
       "{system_skeleton: [https://gitlab.com/huertico/server]}"


upgrade
=======

Boolean variable that defines if a system full upgrade is performed or
not.

If set to *true* a full system upgrade is executed.

This variable is set to *true* by default.

::

   # Including from terminal.
   ansible localhost -m include_role -a name=constrict0r.develbase -K -e \
       "upgrade=false"

   # Including on a playbook.
   - hosts: servers
     roles:
       - role: constrict0r.develbase
         upgrade: false

   # To a playbook from terminal.
   ansible-playbook -i tests/inventory tests/test-playbook.yml -K -e \
       "upgrade=false"


users
=====

List of users to be created. Each non-empty username listed on users
will be created.

This list can be modified by passing an *users* array when including
the role on a playbook or via *–extra-vars* from a terminal.

This variable is empty by default.

::

   # Including from terminal.
   ansible localhost -m include_role -a name=constrict0r.develbase -K -e \
       "{users: [mary, jhon]}"

   # Including on a playbook.
   - hosts: servers
     roles:
       - role: constrict0r.develbase
         users:
           - mary
           - jhon

   # To a playbook from terminal.
   ansible-playbook -i tests/inventory tests/test-playbook.yml -K -e \
       "{users: [mary, jhon]}"


user_skeleton
=============

URL or list of URLs pointing to git skeleton repositories containing
layouts of directories and configuration files.

Each URL on system_skeleton will be checked to see if it points to a
valid git repository, and if it does, the git repository is cloned.

The contents of each cloned repository will then be copied to each
user home directory.

This variable is empty by default.

::

   # Including from terminal.
   ansible localhost -m include_role -a name=constrict0r.develbase -K -e \
       "{user_skeleton: [https://gitlab.com/constrict0r/home]}"

   # Including on a playbook.
   - hosts: servers
     roles:
       - role: constrict0r.develbase
         user_skeleton:
           - https://gitlab.com/constrict0r/home

   # To a playbook from terminal.
   ansible-playbook -i tests/inventory tests/test-playbook.yml -K -e \
       "{user_skeleton: [https://gitlab.com/constrict0r/home]}"


user_tasks
==========

Absolute file path or URL to a *.yml* file containing ansible tasks to
execute.

Each file or URL on this variable will be checked to see if it exists
and if it does, the task is executed.

This variable is empty by default.

::

   # Including from terminal.
   ansible localhost -m include_role -a name=constrict0r.develbase -K -e \
       "{user_tasks: [https://is.gd/vVCfKI]}"

   # Including on a playbook.
   - hosts: servers
     roles:
       - role: constrict0r.develbase
         user_tasks:
           - https://is.gd/vVCfKI

   # To a playbook from terminal.
   ansible-playbook -i tests/inventory tests/test-playbook.yml -K -e \
       "{user_tasks: [https://is.gd/vVCfKI]}"


configuration
=============

Absolute file path or URL to a *.yml* file that contains all or some
of the variables supported by this role.

It is recommended to use a *.yml* or *.yaml* extension for the
**configuration** file.

This variable is empty by default.

::

   # Using file path.
   ansible localhost -m include_role -a name=constrict0r.develbase -K -e \
       "configuration=/home/username/my-config.yml"

   # Using URL.
   ansible localhost -m include_role -a name=constrict0r.develbase -K -e \
       "configuration=https://my-url/my-config.yml"

To see how to write  a configuration file see the *YAML* file format
section.


YAML
****

When passing configuration files to this role as parameters, it’s
recommended to add a *.yml* or *.yaml* extension to the each file.

It is also recommended to add three dashes at the top of each file:

::

   ---

You can include in the file the variables required for your tasks:

::

   ---
   packages:
     - [leafpad, rolldice]

If you want this role to load list of items from files and URLs you
can set the **expand** variable to *true*:

::

   ---
   packages: /home/username/my-config.yml

   expand: true

If the expand variable is *false*, any file path or URL found will be
treated like plain text.


Attributes
**********

On the item level you can use attributes to configure how this role
handles the items data.

The attributes supported by this role are:


item_name
=========

Name of the item to load or create.

::

   ---
   packages:
     - item_name: my-item-name


item_pass
=========

Password for the item to load or create.

::

   ---
   packages:
     - item_pass: my-item-pass


item_groups
===========

List of groups to add users into.

::

   ---
   packages:
     - item_name: my-username
       item_groups: [disk, sudo]


item_expand
===========

Boolean value indicating if treat this item as a file path or URL or
just treat it as plain text.

::

   ---
   packages:
     - item_expand: true
       item_path: /home/username/my-config.yml


item_path
=========

Absolute file path or URL to a *.yml* file.

::

   ---
   packages:
     - item_path: /home/username/my-config.yml

This attribute also works with URLs.


Requirements
************

* `Ansible <https://www.ansible.com>`_ >= 2.8.

* `Jinja2 <https://palletsprojects.com/p/jinja/>`_.

* `Pip <https://pypi.org/project/pip/>`_.

* `Python <https://www.python.org/>`_.

* `PyYAML <https://pyyaml.org/>`_.

* `Requests <https://2.python-requests.org/en/master/>`_.

If you want to run the tests, you will also need:

* `Docker <https://www.docker.com/>`_.

* `Molecule <https://molecule.readthedocs.io/>`_.

* `Setuptools <https://pypi.org/project/setuptools/>`_.


Compatibility
*************

* `Debian Buster <https://wiki.debian.org/DebianBuster>`_.

* `Debian Raspbian <https://raspbian.org/>`_.

* `Debian Stretch <https://wiki.debian.org/DebianStretch>`_.

* `Ubuntu Xenial <http://releases.ubuntu.com/16.04/>`_.


License
*******

MIT. See the LICENSE file for more details.


Links
*****

* `Github <https://github.com/constrict0r/develbase>`_.

* `Gitlab <https://gitlab.com/constrict0r/develbase>`_.

* `Gitlab CI <https://gitlab.com/constrict0r/develbase/pipelines>`_.

* `Readthedocs <https://develbase.readthedocs.io>`_.

* `Travis CI <https://travis-ci.com/constrict0r/develbase>`_.


UML
***


Deployment
==========

The full project structure is shown below:

.. image:: https://gitlab.com/constrict0r/img/raw/master/develbase/deployment.png
   :alt: deployment


Author
******

.. image:: https://gitlab.com/constrict0r/img/raw/master/develbase/author.png
   :alt: author

The Travelling Vaudeville Villain.

Enjoy!!!

.. image:: https://gitlab.com/constrict0r/img/raw/master/develbase/enjoy.png
   :alt: enjoy

