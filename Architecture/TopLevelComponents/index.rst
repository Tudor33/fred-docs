
.. _FRED-Arch-TopComponents:

Top-level components
===============================

.. only:: mode_structure

   .. struct-start

   **Sources:** OLDREPO + NOTES
   | **AoW:** 3 days = *2 days (base on old picture)*

   **Chapter outline:**

   * CORBA servers, CORBA clients, user agents
   * internal interfaces (CORBA IDL)

   .. struct-end

This chapter contains diagrams and brief descriptions of FRED top-level
components and their relationships.

.. _fig-arch-components:

.. rubric:: Diagram of FRED components

.. Note:: Click a component to read its description.

.. raw:: html
    :file: ../_graphics/schema-components.svg

.. rubric:: Legend

* *Violet* components are developed by the CZ.NIC and they are parts
  of the FRED software.
* *White* components are not developed by the CZ.NIC but are required
  for the proper function of the Registry.
* Components coloured otherwise are developed by the CZ.NIC but they are not
  parts of the FRED software.
* *Arrows* signify direct cooperation of components (the arrow points
  at the component which serves the other component); neither the colour
  nor the style of arrows carry any meaning.

.. toctree::
   :maxdepth: 2
   :caption: Chapter TOC

   UserAgents
   CORBAClients
   CORBAServers
   Databases
