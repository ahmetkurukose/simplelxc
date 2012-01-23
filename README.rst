=========
simplelxc
=========

------------------------------
Create an LXC guest in minutes
------------------------------

:Author: Bekir Dogan <bekirdo@gmail.com>
:Date:   2012-01-23
:Copyright: GPLv2
:Version: 0.1
:Manual section: 1
:Manual group: simplelxc


SYNOPSIS
========

  *simplelxc <guest_action> ...*

  *simplelxc template <template_action> ...*


DESCRIPTION
===========

Create new lxc servers on a machine (only Debian for now) as simple as this::

 $ sudo aptitude install simplelxc # install
 $ sudo simplelxc create test1 # create a guest (default wheezy) and run
 $ sudo simplelxc login test1 # login to guest as root

Main objective is to make testing of any program easy on laptops (for now it
works only for Debian) without losing time for these:

 * does not require to manually configure networking on host system,
 * does not require to manually create template,
 * does not require to manually determining ip addresses,
 * does not require to manually configuring any other parameters needed.

Just install simplelxc, create a guest and go.

Main purpose of this project is to handle only simple tasks, so if you want to
manage a production lxc installations you should consoder using plain lxc
userspace control tool (http://lxc.sourceforge.net/) or projects like lxctl
(http://lxc.tl/).


Some simple scenarios
=====================

Testing a new package
---------------------
Testing your brand new package in a whole clean installation.

Testing without hesitation
--------------------------
If you need to compile and test a package but package doesn't supply an unsintall
functionality, what if you don't want that package after installation.

Temporary installations
-----------------------
For example you need a webserver with a lamp installation with phpmyadmin::

 user@host:~$ sudo simplelxc create phpmyadmin
 user@host:~$ sudo simplelxc login phpmyadmin
 root@pma:~# apt-get -y install mysql-server # give an mysql root password
 root@pma:~# apt-get -y install phpmyadmin # installs apache2, php5 and phpmyadmin, answer questions
 root@pma:~# cp /etc/phpmyadmin/apache.conf /etc/apache2/sites-available/phpmyadmin
 root@pma:~# a2ensite phpmyadmin
 root@pma:~# service apache2 reload
 root@pma:~# exit
 user@host:~$ sudo simplelxc info phpmyadmin # learn guest ip address: $guestip
 from browser login: <$guestip>/phpmyadmin

Tooks nearly 10 minutes if you alread have a guest before (so you have a
template before). If thats your second guest like this, this time decreases because
noneed to redownload packages from the archives.


Command line reference
======================

Guest actions
-------------

Guest system operations, you can explicitly use "guest" parameter before action name.

:simplelxc *list*:
    List all available guests.

:simplelxc *create* <guest_name> [<ip>] [<template_name>]:
    Create a guest system and immediately run it. If ip is ommitted use the
    first unused address in given block in NETWORK configuration parameter. If
    template_name is ommitted use default template in configuration file.

:simplelxc *copy* <existing_guest_name> <new_guest_name> [<ip>]:
    Create a new guest from an existing guest by finding and changing ip
    address and host name to their new values.

:simplelxc *login* <guest_name>:
    Login to the guest system as root. This uses ssh to login using preshared
    keys (public key of host system)

:simplelxc *start* <guest_name>:
    Start the guest. Usually you don't need to run a guest manually, they start
    immediately after create.

:simplelxc *stop* <guest_name>:
    Stop a guest.

:simplelxc *restart* <guest_name>:
    Restart a guest system, this is usually required after you configure guest
    system.

:simplelxc *destroy* <guest_name>:
    Delete the guest without any warning. Be careful using this command.

:simplelxc *config* <guest_name>:
    Edit lxc config file of the guest. If you are not sure about what are you
    doing don't change this file.

:simplelxc *chroot* <guest_name>:
    Chroot into the guest root file system, if you are not sure about what you are
    doing you should use "login". See the notes in "*template chroot*".

:simplelxc *info* <guest_name>:
    Show guest's ip, root filesystem, config file and if it is running or not.

Template actions
----------------

Typically you dont need to run these manually. Template's are only root file
systems with hostname as hostaname-<templatename> and ip address as 127.0.0.2,
when creating a guest we just replace this two with original values of the
guest system.

:simplelxc *template list*:
    List all available templates.
           
:simplelxc *template create* [<template_name> [<debian_release_name>]]:
    Create a new release from the given Debian release. If Debian release is
    omitted use given default template name in the simplelxc config. If
    template_name parameter is also omitted use given default template name.
           
:simplelxc *template copy* <existing_template_name> <new_template_name>:
    Create a new template from an existing template. Just copies the directory.
           
:simplelxc *template chroot* <template_name>:
    Chroot into the template, you can modify template using this action. After
    issuing this action you will be in the templates directory structure but
    there is no abstraction for processes and proc and mount not issued in this
    environment. You can return back by "exit" command. But after installing
    any service in a chroot environment Debian immediately runs the service,
    you should stop the service before exit.
           
:simplelxc *template destroy* <template_name>:
    Completely remove the template.

FILES
=====

:/etc/simplelxc:
	Configureation file for simplelxc.

.. To generate a man page from this file: rst2man --strict README simplelxc.1
.. To generate an html page from this file: rst2html --strict README README.html