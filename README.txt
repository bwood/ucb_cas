README ucb_cas-6.x
==================

TABLE OF CONTENTS
-----------------
1.   Purpose
2.   Quick Start
3.   Standard Configuration 
3.1.   Security
3.2.   User Account creation (IMPORTANT)
3.3.   Standard configuration doesn't support "mixed mode authentication"
4.   Requirements
5.   UCB CalNet Registration
6.   Installing
7.   Setup a Calnet-authenticated Administrator
8.   Administrator "back door" for lockouts
9.   Disabling 
10.  Uninstalling 
11.  Configuration Details
12.  Launching your site (Important)
13.  Authors


PURPOSE
-------

UCB CAS is a collection of modules needed use UC Berkeley CalNet
authentication and UC Berkeley LDAP with a Drupal site. Once UCB CAS
is enabled logging into your site via CalNet should "just work."

UCB CAS applies a default configuration to the modules it
installs. This configuration assumes that everyone, including the site
administrators, will login to the site using Calnet/CAS.  See Standard
Configuration.

QUICK START
-----------

1. See: Requirements
2. Install and enable ucb_cas. (More info: Installing)
3. Visit (the unpublicized) login url http://example.com/cas and login
with your calnet id.
4. As User 1 edit the new user that got created in step 2 and assign
it the "administrator" role. (More info: Setup a Calnet-authenticated
administrator)

STANDARD CONFIGURATION
----------------------

The ucb_cas module has made some configuration decisions for you.
These decsions can be overridden by you. See the Configuration Details
section below.

*Security*

By default ucb_cas is configured so that anyone logging into your site
must use UCB Calnet authentication. The reason for this is that
Drupal's standard authentication is insecure, unless used in
conjunction with SSL (https). Drupal standard authentication is
vulnerable to 1) username/password interception (especially if a
wireless network is in use) and 2) session hijacking. (See "Setup a
Calnet-authenticated administrator.")

(Calnet/CAS authentication is not immune to attack on a site running
under http.  In this scenario the CAS ticket could possibly be
hijacked. The combination of https with ucb_cas's default
configuration is a good idea.)

*User account creation (IMPORTANT)*

With the ucb_cas standard configuration, when a user logs in via
CalNet for the first time Drupal will create an account for them and
assign them to the "authenticated user" role. Be very cautious with
assigning the "authenticated user" role any privileges beyond what the
"anonymous user" role has. The best way to manange privileges for your
CalNet users is to create a role like "editor" which is allowed to
create content. You should then review each new person in the
"authenticated user" role and decide whether or not to assign them
your "editor" role.  (The Rules module can be used to send you
automatic emails each time a new user account is created on your
site.)

*Standard configuration doesn't support "mixed mode authentication"*

"Mixed mode authentication" describes then scenario where a site is
configured to allow some users to authenticate using Calnet and others
to authenticate using Drupal standard authentication. The ucb_cas
standard configuration is not geared towards this scenario.  Instead
all users are directed to Calnet for authentication.

Mixed mode authentication presents user experience challenges. The
user must be presented with two different login mechanisms and must
choose between them everytime they login to the site. A module
(ucb_mma) is planned to address this use case.  Until then you may
choose to adjust the ucb_cas default settings, if mma is what you
need. Please remember that you should be running your UCB site using
SSL (https) if you are using Drupal standard authentication.

REQUIREMENTS
------------

Your Drupal site must be registered with the UCB Calnet service. (See:
UCB Calnet Registration)

The modules installed by ucb_cas-6.x are:

cas
cas_attributes (includes cas_ldap)
ldap_integration (includes ldapauth, ldapdata, ldapgroups)

Since UCB CAS installs multiple modules on your site, it's install
process will ensure that those modules do not already exist on your
site.  If conflicting files are found an friendly message will appear
and the installer will abort. If you see this error message when you
enable the module, check the directories that drupal scans for module
files (e.g. sites/all/modules, sites/EXAMPLE/modules,
sites/modules/EXAMPLE/, profiles/EXAMPLE...) for conflicting
modules. If you find conflicts:

1. Disable the conflicting modules at admin/build/modules
2. Remove the files for the conflicting modules from your site
3. Enable UCB CAS
4. Run update.php

Note: It's a good idea to install
http://drupal.org/project/adminrole. See Setup a Calnet-authenticated
administrator

UCB CALNET REGISTRATION
-----------------------

In order to use CalNet authentication, your website must be registered with 
CalNet. Make sure your registration is approved before you install UCB CAS 
on a production site.

Developers working locally may use either localhost or 127.0.0.1, with or 
without a port number, as their site URL without needing to register.

To register, see https://wikihub.berkeley.edu/display/calnet/CAS+Registration.

INSTALLING
----------

1. Download ucb_cas-6.x-X.x.tar.gz to the computer running your Drupal site.
2. Download the Drupal 6 version of token from http://drupal.org/project/token
3. Unarchive these modules in an appropriate directory like sites/all/modules.
4. Enable the module at admin/build/modules.  You ONLY need to enable
the UCB CAS module the other modules will be enabled and configured
for you.
5. Test your site: 

If your site runs at
http://example-dev.berkeley.edu, go to
http://example-dev.berkeley.edu/cas.  You should see the CAS login
page.  When you authenticate successfully you should be returned to
your Drupal site and you should see "Logged in as YOUR NAME."

Go to http://example-dev.berkeley.edu/user.  You should see the email
address that was retrieved from LDAP for your account.

6. See Setup a Calnet-authenticated Administrator

SETUP A CALNET-AUTHENTICATED ADMINISTRATOR
------------------------------------------

User 1 (the account is often named "admin") is the "superuser" on a Drupal
site. Instead of using logging in as this user, you should grant your
Calnet-authenticated user account the administrator role and always
login (via Calnet) with that account.  Here's how:

1. Enable the ucb_cas module
2. Download and enable the adminrole module (http://drupal.org/project/adminrole)
3. Login to your site by visiting http://example.com/cas
4. Drupal creates a user for you upon successful authentication. Visit
http://example.com/admin/user/user, edit your user and assign it the
"administrator role.

Now your Calnet user can do anything that User 1 can do, with one
exception. In Drupal 6 only User 1 can run update.php. Workarounds:

1. Use drush to run update.php: 'drush updatedb' Google for more
information on the incredibly powerful and convenient drush utility.

2. If you are using a best-of-breed Drupal hosting service like
Pantheon (this might apply to Acquia clound too) update.php will be
run for you when you migrate code between your dev, test, live
environments.

3. (WARNING) You could follow the instructions at
http://example.com/update.php?op=info, but keep in mind that this
opens your site possible denial of service attacks among other things.
If you go this route, make sure that you undo that edit.

THE ADMINISTRATOR "BACK DOOR" FOR LOCKOUTS
------------------------------------------

If for some reason you can't login as your calnet-authenticated
administrator, you can login as User 1 at
http://example.com/user/admin_login.

WARNING: The admin_login page is included to help people who are
otherwised locked out of their sites.  It should only be used to
recover from a lockout. It is not a secure form and it suffers from
the Drupal standard authentication vulnerabilities described above.

DISABLING
---------
(This process is tested with drush.)

For maximum flexibilty, disabling ucb_cas does not disable the
companion cas_attributes nor ldap modules. However uninstalling
ucb_cas *will* disable and uninstall the companion modules.

A module is "disabled" when you uncheck it at /admin/build/modules and
submit the form.

A module is "uninstalled" when you 1) disable it and 2) uninstall it
at admin/build/modules/uninstall.

UNINSTALLING
------------
(This process is tested with drush.)

To remove UCB CAS from your site do the following:

1. Disable the UCB CAS module at admin/build/modules. (You do not need
to disable each individual module that UCB CAS installed.)

2. Uninstall the UCB CAS module at admin/build/modules/uninstall.
This step will disable and uninstall each module that UCB CAS
installed.  It will also remove variables that UCB CAS added your
site's variables table.

CONFIGURATION DETAILS
---------------------

In order to make CAS and LDAP work out-of-the-box when you install UCB
CAS, we've made some configuration decisions for you.  These decisions
are aimed at defining "best practices" for using CAS and LDAP with
your Drupal site.  That said, if you don't like our decisions, you can
override them on the appropriate admin page on your site.

CAS Configuration at admin/user/cas:

  *Add CAS Link to Login Forms*
  
    The option "Make CAS login default on login forms" has been set by
    this module. If you have the User Login block enabled, non-logged
    in users wil only have the option to login via CalNet (CAS).
    
    If either of the other two options are selected, non-authenticated
    users will see the option to login using Drupal's standard
    authentication (username and password text inputs boxes).  The
    best practice for UC Berekely sites is to not present users with
    both CalNet and Drupal's standard authentication as login
    options. (See the IMPORTANT note below for more on this.)
  
  *Inital login destination* and *Logout destination*

     You may want to customize these. Feel free...

  *Users cannont change password*

     Unchecking this is very likely to cause confusion.  Users should
     change their passwords via CalNet. See *Change password URL*
     further down.

  *Change Password URL*

     This setting is blank because it can cause confusion. The
     intention of ucb_cas is that all users log into the site using
     Calnet/CAS authentication as opposed to Drupal's standard
     authentication.  Therefore changing your site password would
     require changing your Calnet password (which can be done at
     https://net-auth.berkeley.edu/cgi-bin/krbcpw) and would result in
     your password changing for all Calnet authenticated applications.
     A user presented with a "change password" url might not
     understand the ramifications here.

  *Drupal Login Invitation*

     This setting is blank because it can cause confusion.  It adds a
     link to your login block allowing users to login using Drupal's
     stadandard authentication instead of CalNet.  It's best to
     require ALL of your users to login via CAS and not to give them
     the option of nusing Drupal's authentication.  If you need to
     allow people who don't have a CalNet ID to login to your site,
     you can add a value like "Non-UCB people login here" to this text
     box.

     IMPORTANT: If you allow standard Drupal authentication to your
     site you MUST run your site at an https URL.  Failure to do so is
     a significant security risk yielding multiple
     vulnerabilities. For example, anyone logging into your site from
     a public wireless network can easily have their password stolen.

     (There is a module in the works to facilitte using both CAS and
     standard Drupal authentication on a site. Email
     ist-drupal@lists.berkeley.edu for more information.)

Cas Attributes configuration (admin/user/cas/attributes)

  *Fetch CAS Attributes*

       You can change this to "only when a CAS account is created
       (i.e., the first login of a CAS user)."  That means your site
       will not reflect changes made to LDAP after the user account
       was created on your site.

LAUNCHING YOUR SITE (IMPORTANT)
-------------------------------

	Your site is using the servers ldap-test.berkeley.edu and
	auth-test.berkeley.edu.  These are the correct servers to use
	for site development and testing.  When you make your site
	live, you should change these servers to ldap.berkeley.edu and
	auth.berkeley.edu. Make these changes at:

        admin/user/cas
        admin/user/cas/attributes

        (A module to help automate this is in the works.)

AUTHORS
-------
Brian Wood, UC Berkeley, http://drupal.org/user/164217

CONTRIBUTORS
------------
Caroline Boyden, UC Berkeley
