
.. _FRED-Concept-Merger:

Contact merger
==============

The purpose of contact merger is to minimize duplicate contacts
in the Registry.

The core of the merger is the :ref:`merge operation <merge-operation>`
which allows to merge a pair of identical and merge-able contacts,
from a *source contact* to a *destination contact*.
If the contacts are linked to other objects, then the *source contact*\ (s) is (are)
replaced with the *destination contact* in every linked object.
The *source contact*\ (s) is (are) then deleted from the Registry.

The Registry operator may choose a pair of duplicate contacts themself and
merge just the two of them if the two contacts are :ref:`merge-able <mergeable-contacts>`.
(See the task :ref:`contact-merge-manual`.)

The other option is to use the automatic merge procedure which can select
a whole set of duplicate contacts automatically and merge them
into a single *destination contact*. The *destination contact* is determined
by filtering the set with various criteria (see :ref:`merge-auto-criteria`),
which make the outcome the best possible choice. The other contacts in the set
are treated as *source contacts*.
(This task can be set as a :ref:`periodic task <cronjob-contact-merger>`.)

In both cases, the contacts must be managed by the same registrar, however,
they are replaced in linked objects even if the linked objects have different
registrars.

.. _merge-auto-identity:

Identical contacts
------------------

For contacts to be considered identical by the merger, they must have
the following attributes identical:

* current designated registrar: ``object.clid``
* name: ``contact.name`` [#trim]_
* organization: ``contact.organization`` [#trim]_
* address (permanent): ``contact.street1``, ``contact.street2``, ``contact.street3``,
  ``contact.city``, ``contact.stateorprovince``, ``contact.postalcode``,
  ``contact.country`` [#trim]_
* email: ``contact.email`` [#trim]_
* notification email: ``contact.notifyemail`` [#trim]_
* fax: ``contact.fax`` [#trim]_
* telephone: ``contact.telephone`` [#trim]_
* identity document type: ``contact.ssntype``
* identity document number: ``contact.ssn`` [#trim]_
* VAT: ``contact.vat`` [#trim]_
* disclose flags: ``contact.disclosename``, ``contact.discloseorganization``,
  ``contact.discloseaddress``, ``contact.disclosetelephone``,
  ``contact.disclosefax``, ``contact.discloseemail``, ``contact.disclosevat``,
  ``contact.discloseident``, ``contact.disclosenotifyemail``
* user's preference of sending domain expiration letters: ``contact.warning_letter``
* and for each associated contact address of any type (\ ``MAILING``, ``BILLING``,
  ``SHIPPING``, ``SHIPPING_2``, ``SHIPPING_3``):

   * :samp:`{contact_address}.company_name` [#trim]_
   * :samp:`{contact_address}.street1` [#trim]_
   * :samp:`{contact_address}.street2` [#trim]_
   * :samp:`{contact_address}.street3` [#trim]_
   * :samp:`{contact_address}.city` [#trim]_
   * :samp:`{contact_address}.stateorprovince` [#trim]_
   * :samp:`{contact_address}.postalcode` [#trim]_
   * :samp:`{contact_address}.country` [#trim]_

.. [#trim] These values are trimmed before they are compared. Trimming means
   that spaces at the beginning and at the end of strings are removed.
   Trimming does not affect any other whitespace characters nor spaces that
   separate words.

.. _mergeable-contacts:

Merge-able contacts
-------------------

Contacts must be :ref:`identical <merge-auto-identity>` and comply with the
following conditions:

* the *source contact*\ (s):
   * must not be administratively blocked (\ ``serverBlocked`` status active), and
   * must not have the ``serverDeleteProhibited`` status active, and
   * must not belong to a mojeID account (\ ``mojeidContact`` status active), and
   * must not have the ``contactInManualVerification`` status active, and
   * must not have the ``contactFailedManualVerification`` status active,
* and the *destination contact*:
   * must not be administratively blocked (\ ``serverBlocked`` status active), and
   * must not have the ``contactInManualVerification`` status active, and
   * must not have the ``contactFailedManualVerification`` status active,
* and registrable objects linked to the *source contact*:
   * must not have the ``serverBlocked`` status active, and
   * must not have the ``serverUpdateProhibited`` status active.

.. Note:: The rules for identity and merge-ability are hard-coded.

.. _merge-operation:

Merge operation
---------------

The procedure of merging a pair of duplicate contacts performs as follows:

#. Checks that the contacts are :ref:`merge-able <mergeable-contacts>`.
#. In objects linked to the *source contact*, replaces the *source contact*
   with the *destination contact* (using update operations).
#. If the *source contact* has had the ``contactPassedManualVerification``
   status active, sets it on the *destination contact*.
#. Deletes the *source contact* from the Registry.
#. Generates new authinfo for the *destination contact*.
#. Generates poll messages for changes made in the step 2.

.. _merge-auto-criteria:

Selection of the *destination contact* in an automatic merger
-------------------------------------------------------------

Because the detection of duplicates is automatic, the Registry must also select
the *destination contact*, into which the merge will result and
which will be used to replace the duplicate contacts in linked objects.

The contact of the best qualities is selected according to the following criteria
evaluated in this order [#default]_:

* contact is identified,
* contact is conditionally identified,
* contact handle complies with the syntax for mojeID handles,
* contact has most domains linked (as a :term:`holder` or administrative contact),
* contact has most objects linked (domains, name-server sets or key sets),
* contact has been updated most recently,
* contact has been created most recently.

.. * contact's designated registrar is not CZ.NIC, // left out intentionally

The contact that matches the most of the criteria, is the *destination contact*.
If more than one contact meets all of these criteria, the *destination contact*
is chosen from them randomly.

.. [#default] This is the default setting used in CZ.NIC. The Registry operator
   may modify which criteria will be applied and in what order, in a command option.


.. only:: domain_browser

   .. _merge-db-identity:

   Identical contacts in the Domain Browser
   ----------------------------------------

   For two contacts to be considered identical by the merger:

   * the *source contact* and the *destination contact* must have the following
     attributes identical:

      * name: ``contact.name`` [#trim]_
      * organization: ``contact.organization`` [#trim]_
      * address: ``contact.street1``, ``contact.street2``, ``contact.street3``,
        ``contact.city``, ``contact.stateorprovince``, ``contact.postalcode``,
        ``contact.country`` [#trim]_
      * email: ``contact.email`` [#trim]_

   * if this attribute is filled in the *destination contact*, then the *source*
     contact attribute must either contain the same value or be empty:

      * identity document type: ``contact.ssntype``
      * identity document number: ``contact.ssn`` [#trim]_
      * VAT: ``contact.vat`` [#trim]_
