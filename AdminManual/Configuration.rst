
.. _FRED-Admin-Config:

Configuration
=========================

.. only:: mode_structure

   .. struct-start

   **Sources:** NOTES + IVIEW + translate
   | **AoW:** done & unplanned

   **Chapter outline:**

   * Overview of executables and the default config.files
      * Main and supporting programs with short descriptions
      * List of config.files with default locations
   * Configurable database values
      * table:enum_parameters

   .. struct-end

This chapter contains an overview of executable files that are a part of the FRED
and the location and names of their default configuration files.

There are also some :ref:`configurable database values <config-db>`,
which you should consider revising.

.. Note:: The descriptions of configurable values are contained in the default
   configuration files.

.. contents:: Chapter TOC
   :local:
   :backlinks: none

Security notice
---------------

.. Important::
   Always adapt access control information (especially user names and passwords)
   for the use on production!

Executables
-------------

The overview of executable files should give you an idea about the names
of the FRED components that actually can be launched,
and about the settings that must be configured on the system level.
The mentions of the default configuration files are there to guide you
to locations where these settings are made.

The default configuration files are located in :file:`@PREFIX@/etc/fred`.

CORBA servers
^^^^^^^^^^^^^

The CORBA servers are located in a system-binaries directory (:file:`@PREFIX@/sbin`).

:program:`Systemd` startup scripts are included with FRED packages
(e.g. :file:`/lib/systemd/system/fred-rifd.service`). A startup
command is introduced with :code:`ExecStart=` in the scripts.

.. _config-servers-cpp:

C++ daemons
~~~~~~~~~~~
These daemons provide operations via the IDL interface implemented in C++.

In the default deployment scheme, all daemons run
on a single machine and they share an all-in-one configuration file
named :file:`server.conf`.

In the deployment scheme adopted in the CZ.NIC, separate configuration files
are used for each daemon, therefore they are listed with the daemons below
and marked [CZ.NIC].

.. Note::

   If you decide to deploy servers on several machines,
   you must specify the common configuration settings on each machine, namely
   the sections: ``[database]``, ``[nameservice]``, ``[log]`` and ``[registry]``,
   plus the daemon-specific settings for each daemon that runs on that machine.

* :file:`fred-adifd` – administration interface daemon – operations for
  administration (WebAdmin)

   * standalone configuration file [CZ.NIC]: :file:`/etc/fred/fred-adifd.conf`

* :file:`fred-rifd` – registrar interface daemon – operations for the
  EPP-protocol Apache module (mod-eppd)

   * standalone configuration file [CZ.NIC]: :file:`/etc/fred/fred-rifd.conf`

* :file:`fred-pifd` – public interface daemon – operations for Unix whois,
  web whois, RDAP and contact verification

   * standalone configuration file [CZ.NIC]: :file:`/etc/fred/fred-pifd.conf`

* :file:`fred-rsifd` – record statement interface daemon – operations
  for the provision of registry record statements

   * standalone configuration file [CZ.NIC]: :file:`/etc/fred/fred-rsifd.conf`

* :file:`fred-akmd` – automated keyset management daemon – operations
  for managing keysets automatically

   * standalone configuration file [CZ.NIC]: :file:`/etc/fred/fred-akmd.conf`

* :file:`fred-accifd` – accounting daemon – operations for payment pairing

   * standalone configuration file [CZ.NIC]: :file:`/etc/fred/fred-accifd.conf`

* :file:`fred-msgd` – messaging daemon – operations for sending SMS text
  messages and paper letters

   * standalone configuration file [CZ.NIC]: :file:`/etc/fred/fred-msgd.conf`

* :file:`fred-logd` – logging daemon (logger) – operations for the logging
  of user activity

   * standalone configuration file [CZ.NIC]: :file:`/etc/fred/fred-logd.conf`

* :file:`fred-mifd` – mojeID daemon (extension) – operations for the mojeID
  service

   * standalone configuration file [CZ.NIC]: :file:`/etc/fred/fred-mifd.conf`

* :file:`fred-dbifd` – domain browser daemon (extension) – operations for
  the Domain Browser web application

   * standalone configuration file [CZ.NIC]: :file:`/etc/fred/fred-dbifd.conf`


Configuration parameters can also be passed as *command-line arguments* to the daemons,
see ``<daemon> --help``.

.. _config-servers-omni:

.. rubric:: ORB parameters

Additionally, each daemon accepts ORB parameters which it hands over to its ORB.

.. Important:: **OmniORB minimum**

   You need to override the following omniORB settings when running FRED servers:

   * native character encoding – to encode/decode text data for transmission with UTF-8;
     this can be set on the command-line as :code:`<daemon> -ORBnativeCharCodeSet UTF-8` or
     in the ORB configuration file (possibly :file:`/etc/omniORB.cfg`)
     as the ``nativeCharCodeSet`` variable,

   * endpoint address – to specify a *port* on which a server listens for CORBA calls;
     a unique port must be specified for each server; this must be set on the command-line,
     such as |br| :code:`<daemon> -ORBendPoint giop:tcp::<port>`.

To list all possible ORB parameters, run ``omniNames -help``;
see also `omniORB configuration <http://omniorb.sourceforge.net/omni42/omniORB/omniORB004.html>`_
for a more detailed explanation of the ORB parameters.

.. _config-servers-py:

Python daemon(s)
~~~~~~~~~~~~~~~~
This daemon provides operations via the IDL interface implemented in Python.

In the default deployment scheme, the daemon loads all modules and runs
in a single process (on a single machine) and all modules share an all-in-one
configuration file named :file:`pyfred.conf`.

In the deployment scheme adopted in the CZ.NIC, separate configuration files
are used for each daemon, therefore they are listed with the daemons below
and marked [CZ.NIC].

* :file:`fred-pyfred` – a framework that integrates several Python CORBA
  servers as modules:

   * :file:`genzone` – operations for generating zone files,

      * standalone configuration file [CZ.NIC]: :file:`/etc/fred/pyfred-genzone.conf`

   * :file:`mailer` – operations for email assembly and dispatch,

      * standalone configuration file [CZ.NIC]: :file:`/etc/fred/pyfred-mailer.conf`

   * :file:`filemanager` – operations for managing files (mostly email attachments),

      * standalone configuration file [CZ.NIC]: :file:`/etc/fred/pyfred-filemanager.conf`

   * :file:`techcheck` – operations for running technical checks of name servers.

      * standalone configuration file [CZ.NIC]: :file:`/etc/fred/pyfred-techcheck.conf`

.. _config-webadmin:

Web administration server
^^^^^^^^^^^^^^^^^^^^^^^^^

* :file:`fred-webadmin` – server for the web administration of the FRED

Default config.file: :file:`@PREFIX@/etc/fred/webadmin_cfg.py`

.. _config-cliutils:

CLI utilities
^^^^^^^^^^^^^
Located in :file:`@PREFIX@/bin`

* :file:`cdnskey-scanner` – CDNSKEY resource record mining utility (no config. file)
* :file:`filemanager_client` – Inserting a new file into the system
  (uses :file:`pyfred.conf`)
* :file:`fred-akm` – Automated keyset management client (:file:`/etc/fred/fred-akm.conf`)
* :file:`fred-admin` – Automated administration tasks (:file:`server.conf`),
  especially those performed periodically,
  see also :ref:`Periodic tasks <FRED-Admin-PeriodicTasks>`
* :file:`fred-client` – Tool for registrars (:file:`/etc/fred/fred-client.conf`)
* :file:`fred-doc2pdf` – Rendering the standard input (RML) into the PDF
  (:file:`/etc/fred/fred-doc2pdf.conf`)
* :file:`genzone_client` – Generating zones (:file:`/etc/fred/genzone.conf`)
* :file:`mailer_client` – Sending email (:file:`pyfred.conf`)
* :file:`simple_stats.py` – Statistics (???)
* :file:`techcheck_client` – Launching technical checks (:file:`pyfred.conf`)
* :file:`transproc` – Processing the transcripts of bank transactions
  (:file:`/etc/fred/transproc.conf`)

Database management
~~~~~~~~~~~~~~~~~~~
* :file:`fred-dbmanager` (in :file:`@PREFIX@/sbin`) – Basic database management
  script (no config. file)
* :file:`create_parts` and :file:`drop_parts` (in :file:`@PREFIX@/bin`)
  – :ref:`Logger partitions <logger-partitions>` maintenance scripts (example config in
  :file:`@PREFIX@/share/doc/fred-logger-maintenance/examples/logger.conf.example`)

Database search
~~~~~~~~~~~~~~~
Located in :file:`@PREFIX@/bin`

* :file:`filemanager_admin_client` – search in managed files
* :file:`mailer_admin_client` – search in sent email
* :file:`techcheck_admin_client` – search in executed technical checks

Apache modules
^^^^^^^^^^^^^^

Configuration of FRED Apache modules and FRED sites can be found in Apache
configuration subdirectories, usually under :file:`/etc/apache2/`.

Django PAIN
^^^^^^^^^^^^^

Configuration of this Django utility is in the :file:`settings.py`
and described within project source.

.. _config-db:

Database tables
---------------

Some parts of the Registry behaviour can be configured by modifying or adding
values in certain database tables.

.. Important:: **If you reconfigure the system using database tables, remember
   to examine changes in the database component before upgrading the FRED!**

   Sometimes we need to edit the database configuration for ourselves and these
   changes are added to a database upgrade script, which would overwrite
   your settings.
   Therefore you should either **backup** your current database tables to recover
   your settings after the upgrade, **or adapt** the upgrade script directly,
   so that you don't lose your settings.

.. contents:: Section TOC
   :local:
   :backlinks: none

.. _config-dbparams:

Life cycle parameters
^^^^^^^^^^^^^^^^^^^^^

A part of database configuration relates to the :doc:`life cycle </Concepts/LifeCycle/index>`
of :term:`registrable object`\ s. It states e.g.
when to send a notification to a contact before their domain expires or
for how long a domain is protected before it can be re-registered.

There is a table dedicated to this kind of configuration called ``enum_parameters``.
The parameters in this table can be changed using the following command:

.. code-block:: shell

   fred-admin --enum_parameter_change --parameter_name=<name> --parameter_value=<value>

Parameters that affect **all types** of registrable objects:

* ``regular_day_procedure_period`` – an hour in the day when the :ref:`regular
  procedure <cronjob-regular>` is run (24-hour system, 0 means 00:00, 14 means
  14:00 etc.),
  default: ``0``

.. ??? the value must agree with the :ref:`CRON job setting <cronjob-regular>`,

* ``regular_day_procedure_zone`` – time zone for periodic tasks,
  default: ``Europe/Prague``

   .. Important:: It is necessary to adapt the time zone to your area!

   The format of a value is the standardized PostgreSQL name of a time zone
   which can be found either in the Postgres table ``pg_timezone_names``
   (the *name* column) or `in this Wikipedia list
   <https://en.wikipedia.org/wiki/List_of_tz_database_time_zones>`_
   (the *TZ* column).

* ``roid_suffix`` – suffix used in **r**\ epository **o**\ bject **id**\ entifiers
  (see also :term:`ROID`), which are :doc:`assigned to registrable objects
  </EPPReference/ManagedObjects/Common>` by the Registry,
  default: ``EPP``

Parameters that affect **only domains**:

* ``expiration_dns_protection_period`` – for how many days after expiration
  is a domain still generated in a zone, integer,
  default: ``30``
* ``expiration_letter_warning_period`` – how many days after expiration
  is the owner warned about domain deletion, integer,
  default: ``34``
* ``expiration_notify_period`` – how many days before a domain expiration
  is the owner notified about the expiration, negative integer,
  default: ``-30``
* ``expiration_registration_protection_period`` – for how many days
  after expiration is a domain protected before it is deleted and
  can be re-registered, integer,
  default: ``61``
* ``outzone_unguarded_email_warning_period`` – for how many days after expiration
  may customer support enter additional email addresses in Daphne before the system
  starts sending warnings about domain exclusion from the zone to them, integer,
  default: ``25``
* ``regular_day_outzone_procedure_period`` – an hour in the day when the outzone
  procedure is run (24-hour system, 0 means 00:00, 14 means 14:00 etc.),
  default: ``14``

* ``validation_notify1_period`` :sup:`ENUM domains` – how many days before validation
  expiry the owner should be notified for the first time, negative integer,
  default: ``-30``
* ``validation_notify2_period`` :sup:`ENUM domains` – how many days before validation
  expiry the owner should be notified for the second time, negative integer,
  default: ``-15``

.. ??? regular_day_outzone_procedure_period
   the value must agree with a CRON job setting? (not documented),

.. Important:: The system does not verify that the time intervals
   follow one another correctly. Double check with the :doc:`life cycle
   </Concepts/LifeCycle/index>`!

Parameters that affect **only non-domain objects**:

* ``handle_registration_protection_period`` – for how many months is a handle
  (of a contact, nsset or keyset) protected before it can be re-registered,
  default: ``2``
* ``object_registration_protection_period`` – how many months an object
  (nsset, keyset) must be unedited and unassigned to be considered idle and
  marked for deletion,
  default: ``6``

.. todo:: Request usage configurables
   :class: todo-backlog

   * table:request_fee_parameter (.count_free_base+.count_free_per_domain)
   * table:request_fee_registrar_parameter (.request_price_limit)

.. _config-dn:

Domain name format validation
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The implemented rules for domain-name formatting are enumerated in the table
\ ``enum_domain_name_validation_checker``. The Registry operator can turn them on or off
by adding or removing an association in the table ``zone_domain_name_validation_checker_map``,
such as:

.. code-block:: sql
   :caption: Example of SQL insertion of format association with a zone

   INSERT INTO zone_domain_name_validation_checker_map (checker_id, zone_id)
      values (2, 1);

where ``checker_id`` is an id of a formatting rule and ``zone_id`` is an id of a zone.

For a domain name to be valid, it must comply with all rules assigned to its zone.

:ref:`Further restrictions on domain names <config-restrict-dn>` may be required
by the domain blacklist.

.. _config-handles:

Handle format validation
^^^^^^^^^^^^^^^^^^^^^^^^

The format of handles can be prescribed for non-domain object types—contacts,
nssets and keysets—with settings in two database tables:

* ``regex_handle_validation_checker`` – contains all patterns but does not
  specify which of them have to be matched,
* ``regex_object_type_handle_validation_checker_map`` – determines which
  patterns will have to be matched for which object types.

To configure a new allowed pattern, connect to the database and insert a new
regular expression into the ``regex_handle_validation_checker`` table, such as:

.. code-block:: sql
   :caption: Example of SQL insertion of a handle format pattern

   INSERT INTO regex_handle_validation_checker (regex, description)
      values ('^[Cc]', 'must start with the letter c or C');

Now, associate the new pattern to object types using the map table, such as:

.. code-block:: sql
   :caption: Example of SQL insertion of format association with an object type

   INSERT INTO regex_object_type_handle_validation_checker_map (type_id, checker_id)
      values (1, 3);

where ``checker_id`` is the id of our new pattern and ``type_id`` is the id
of the desired object type from the ``enum_object_type`` table,
in our case contact. A regex pattern can be associated with several object types.
In our example, the pattern will make sure that contact handles start with the letter c or C.

In case an invalid regular expression was set up in the database,
then the corresponding :samp:`check_{object}`, :samp:`create_{object}` and :samp:`info_{object}`
operations will respond with the ``2400 Command failed`` result code.

For a handle to be valid, it must match all patterns assigned to its object type.

If a handle is not valid according to db settings, the EPP client receives
a response with the ``2005 Parameter value syntax error`` result code.

.. Important::

   It may be necessary to adapt the XML schemas of EPP as well.
   Handle formats are defined in the :file:`fredcom-1.2.1.xsd` file with the
   following *simple types*:

      * ``objIDCreateType`` – handles of objects being created – must correspond
        with the current db setting,
      * ``objIDType`` – handles of objects occurring elsewhere – must correspond
        with the current setting and allow all historical db settings
        so that handles conforming previous formatting rules can still be used,
      * ``objIDChgType`` – same as ``objIDType`` but allowing an empty string.

   If a handle is not valid according to XML schemas, the EPP client receives
   a response with the ``2001 Command syntax error`` result code due to failed
   XML validation.

.. _config-restrict-dn:

Restricting domain names
^^^^^^^^^^^^^^^^^^^^^^^^

A forbidden pattern for domain names can be configured by inserting a new pattern
with a validity period (i.e. when the pattern is applicable)
into the ``domain_blacklist`` table, such as:

.. code-block:: sql
   :caption: Example of SQL insertion in domain blacklist (minimum query)

   INSERT INTO domain_blacklist (regexp, valid_from, reason)
      VALUES ('^..\.cz$', '2017-07-01 07:00:00', 'forbid 2-char length in cz zone');

The syntax for these patterns is `POSIX regular expressions
<https://www.postgresql.org/docs/current/static/functions-matching.html#POSIX-SYNTAX-DETAILS>`_
and pattern matching is case insensitive (the ``~*`` operator).

Temporal validity (\ ``valid_from``–\ ``valid_to``) must be specified for each pattern,
however the ``valid_to`` datetime can be left empty and then the validity is unbounded
(the pattern is applicable forever).

The patterns can be used in various ways:

* to list forbidden words, for example: pattern ``good|bad|ugly`` will refuse
  registrations of any domain names that contain one of the words "good", "bad" or "ugly",
* to force length of domain names, for example: pattern ``^..\.cz$`` will refuse
  registrations of 2-character domain names in the cz TLD,
* or any other that regular expressions can express.

For a domain name to be valid, it must not match any pattern that is currently applicable.

.. _config-contact-reminder:

Adding a registrar memo to annual reminders
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The :ref:`email template <email-type-contact-reminder>`
for :ref:`annual reminders of contacts <contact-reminder>`
allows to include a memo from the designated registrar in the email
and a custom email address to which the contact can reply.

The Registry operator may insert this information
into the ``reminder_registrar_parameter`` table:

* ``registrar_id`` – identify the registrar,
* ``template_memo`` – enter the memo in plain text;
  use the ``~@~`` sequence to separate language variants
  (local language first, English second); whitespace is preserved,
* ``reply_to`` – enter an email address for the Reply-To header.

This must be done manually with an SQL insert.

.. _config-contact-disclosure:

.. include:: ConfigDisclosure.rst
