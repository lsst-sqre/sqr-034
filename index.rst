..
  Technote content.

  See https://developer.lsst.io/restructuredtext/style.html
  for a guide to reStructuredText writing.

  Do not put the title, authors or other metadata in this document;
  those are automatically added.

  Use the following syntax for sections:

  Sections
  ========

  and

  Subsections
  -----------

  and

  Subsubsections
  ^^^^^^^^^^^^^^

  To add images, add the image file (png, svg or jpeg preferred) to the
  _static/ directory. The reST syntax for adding the image is

  .. figure:: /_static/filename.ext
     :name: fig-label

     Caption text.

   Run: ``make html`` and ``open _build/html/index.html`` to preview your work.
   See the README at https://github.com/lsst-sqre/lsst-technote-bootstrap or
   this repo's README for more info.

   Feel free to delete this instructional comment.

:tocdepth: 1

.. Please do not modify tocdepth; will be fixed when a new Sphinx theme is shipped.

.. sectnum::

.. TODO: Delete the note below before merging new content to the master branch.


TL;DR
=====
The EFD is managed by Sasquatch, a service of the `Rubin Science Platform`_ for recording, displaying, and alerting on Rubin’s engineering data.
Sasquatch is currently deployed at the Summit, USDF and test stands through `Phalanx`_.

The main entry points are the Chronograf UI and the EFD Python client from where users can query the EFD database.

+------------------------+---------------------------------------------------+--------------------------+----------------+
| **Sasquatch Instance** | **Chronograf UI**                                 | **EFD Client alias**     | **VPN access** |
+========================+===================================================+==========================+================+
| :ref:`Summit<summit>`  | https://summit-lsp.lsst.codes/chronograf          | ``summit_efd``           | Chile VPN      |
+------------------------+---------------------------------------------------+--------------------------+----------------+
| :ref:`USDF<usdf>`      | https://usdf-rsp.slac.stanford.edu/chronograf     | ``usdf_efd``             | Not required   |
+------------------------+---------------------------------------------------+--------------------------+----------------+
| :ref:`USDF dev<usdfd>` | https://usdf-rsp-dev.slac.stanford.edu/chronograf | ``usdf_efd_dev``         | Not required   |
+------------------------+---------------------------------------------------+--------------------------+----------------+
| :ref:`TTS<tts>`        | https://tucson-teststand.lsst.codes/chronograf    | ``tucson_teststand_efd`` | NOIRLab VPN    |
+------------------------+---------------------------------------------------+--------------------------+----------------+
| :ref:`BTS<bts>`        | https://base-lsp.lsst.codes/chronograf            | ``base_efd``             | Chile VPN      |
+------------------------+---------------------------------------------------+--------------------------+----------------+

.. note::

  If you need help, please drop a line on the ``#com-square-support`` LSSTC Slack channel.


.. _Rubin Science Platform: https://rsp.lsst.io
.. _Phalanx: https://phalanx.lsst.io


Sasquatch Instances
===================

In this section we describe the Sasquatch instances that host the EFD database.

In addition to Chronograf and the EFD client, other services available are the InfluxDB HTTP API, the Kafdrop UI and the Kafka brokers, the Kafka bootstrap server, and the Kafka REST proxy API.

.. _summit:

Summit
------

Production instance running at the Summit (Chile).
The Summit EFD data is replicated to the USDF production instance for project wide access.

Intended audience: Observers and Commissioning team.

- Chronograf: ``https://summit-lsp.lsst.codes/chronograf``
- InfluxDB HTTP API: ``https://summit-lsp.lsst.codes/influxdb``
- Kafdrop UI: ``https://summit-lsp.lsst.codes/kafdrop``
- Kafka brokers:

  - ``sasquatch-summit-kafka-0.lsst.codes``
  - ``sasquatch-summit-kafka-1.lsst.codes``
  - ``sasquatch-summit-kafka-2.lsst.codes``

- Kafka bootstrap server: ``sasquatch-summit-kafka-bootstrap.lsst.codes``
- Kafka REST proxy API: ``https://summit-lsp.lsst.codes/sasquatch-rest-proxy``

.. _usdf:

USDF
----

Production instance running at SLAC.
It has a replica of the Summit EFD data.

Intended audience: Project staff.

- Chronograf: ``https://usdf-rsp.slac.stanford.edu/chronograf``
- InfluxDB HTTP API: ``https://usdf-rsp.slac.stanford.edu/influxdb``
- Kafdrop UI: ``https://usdf-rsp.slac.stanford.edu/kafdrop``
- Kafka brokers:
  (not yet available)
- Kafka boostrap server:
  (not yet available)
- Kafka REST proxy API: ``https://usdf-rsp.slac.stanford.edu/sasquatch-rest-proxy``

.. _usdfd:

USDF dev
--------

Development instance running at SLAC.
It has a replica of the Tucson test stand instance.

Intended audience: Project staff.

- Chronograf: ``https://usdf-rsp-dev.slac.stanford.edu/chronograf``
- InfluxDB HTTP API: ``https://usdf-rsp-dev.slac.stanford.edu/influxdb``
- Kafdrop UI: ``https://usdf-rsp-dev.slac.stanford.edu/kafdrop``
- Kafka brokers:
  (not yet available)
- Kafka boostrap server:
  (not yet available)
- Kafka REST proxy API: ``https://usdf-rsp-dev.slac.stanford.edu/sasquatch-rest-proxy``

.. _tts:

Tucson Test Stand (TTS)
-----------------------

Development instance running at the Tucson test stand.

Intended audience: Telescope & Site team.

- Chronograf: ``https://tucson-teststand.lsst.codes/chronograf``
- InfluxDB HTTP API: ``https://tucson-teststand.lsst.codes/influxdb``
- Kafdrop UI: ``https://tucson-teststand.lsst.codes/kafdrop``
- Kafka brokers:

  - ``sasquatch-tts-kafka-0.lsst.codes``
  - ``sasquatch-tts-kafka-1.lsst.codes``
  - ``sasquatch-tts-kafka-2.lsst.codes``

- Kafka bootstrap server: ``sasquatch-tts-kafka-bootstrap.lsst.codes``
- Kafka REST proxy API: ``https://tucson-teststand.lsst.codes/sasquatch-rest-proxy``


.. _bts:

Base Test Stand (BTS)
---------------------

Standalone instance running at the Base facility (Chile).

Intended audience: Telescope & Site team.

- Chronograf: ``https://base-lsp.lsst.codes/chronograf``
- InfluxDB HTTP API: ``https://base-lsp.lsst.codes/influxdb``
- Kafdrop UI: ``https://base-lsp.lsst.codes/kafdrop``
- Kafka brokers:

  - ``sasquatch-base-kafka-0.lsst.codes``
  - ``sasquatch-base-kafka-1.lsst.codes``
  - ``sasquatch-base-kafka-2.lsst.codes``

- Kafka bootstrap server: ``sasquatch-base-kafka-bootstrap.lsst.codes``
- Kafka REST proxy API: ``https://base-lsp.lsst.codes/sasquatch-rest-proxy``



Introduction
============

In this technote, we describe the Engineering and Facilities Database (EFD) and tools to access the data.

The main EFD instance runs at the Summit recording observatory telemetry, events and commands enabling real-time monitoring during the observations.

At the Summit the nominal retention period for the EFD data is 30 days.

The EFD data is also replicated to the USDF in quasi realtime (within a few seconds).

The USDF EFD is meant to be the place Rubin Observatory staff can connect and perform their analysis without interfering with the Summit operations, including trending analysis on historical data.


.. figure:: /_static/efd_summit.svg
   :name: EFD components for the Summit/test stand instances.
   :target: _static/efd_summit.svg

   A typical Sasquatch deployment with Kafka, InfluxDB Sink connector, InfluxDB, Chronograf and Kapacitor components.

See :sqr:`068` :cite:`SQR-068` for more information on the Sasquatch architecture.


SAL Kafka producer
==================

The `SAL Kafka`_ producers forward messages from SAL components (CSCs) to Kafka.

Each `CSC`_ has a number of command, events and telemetry topics that are mapped to Kafka topics.

In Sasquatch, the Kafdrop UI can be used to browse the Kafka topics, for example https://summit-lsp.lsst.codes/kafdrop.

.. _SAL Kafka: https://ts-salkafka.lsst.io/
.. _CSC: https://ts-xml.lsst.io/index.html#csc-table


InfluxDB Sink connector
=======================

The InfluxDB Sink connector is the component responsible for consuming the Kafka topics and writing the data to InfluxDB, a time series database.

`kafka-connect-manager`_ manages the InfluxDB Sink connector and other connectors in Sasquatch.

.. _kafka-connect-manager: https://kafka-connect-manager.lsst.io


InfluxDB
========

InfluxDB is an open-source `time series database`_ optimized for efficient storage and analysis of time series data.

A good overview of InfluxDB and how it compares to a more tradional relational database is given `here`_.

InfluxDB organizes the data in measurements (equivalent to a table in a relational database), fields (metrics and events) and tags (metadata around metrics and events).

The important concept for the EFD is that SAL topics are mapped to InfluxDB measurements and all the fields in a SAL topic are mapped to InfluxDB fields.
Essentially we have one time series for each SAL topic, where the point in that time series corresponds to the field set of the SAL topic.

The timestamp used to index the EFD database in InfluxDB is the ``private_efdStamp`` field which corresponds to the SAL ``private_sndStamp`` field converted from TAI to UTC.

In the EFD we write timestamps with microsseconds precision, which seems exagerated but that's important to differentiate events that are very close to each other (e.g. debug events).

InfluxDB provides an SQL-like query language called `InfluxQL`_ and a more powerful data scripting language called `Flux`_.
Both languages can be used in Chronograf for data exploration and for creating dashboards.

.. _time series database: https://www.influxdata.com/time-series-database/
.. _here: https://docs.influxdata.com/influxdb/v1.8/concepts/crosswalk/
.. _InfluxQL: https://docs.influxdata.com/influxdb/v1.8/query_language/
.. _Flux: https://docs.influxdata.com/influxdb/v1.8/flux/


The EFD client
--------------

The EFD client is a Python client to access EFD data from a notebook in the RSP.

For, example a the Summit you can instantiate the EFD client using:

.. code::

   from lsst_efd_client import EfdClient
   efd = EfdClient('summit')

   await efd.get_topics()

where `summit` is the alias to connect to the EFD database at the Summit.

Learn more about the helper methods implemented from the `EFD client documentation`_ and from the example notebooks.

.. _EFD client documentation: https://efd-client.lsst.io


Example notebooks
-----------------

-


Chronograf
==========

Chronograf is the main UI for data exploration, for creating dashboards and alerts on the EFD data.


.. `Chronograf documentation`_.

.. _Chronograf documentation: https://docs.influxdata.com/chronograf/v1.9


Chronograf shared account
-------------------------

There is a shared account ``chronograf-viewer`` user that is meant to be used for the control room displays, the password can be found in LSST IT/SQuaRE 1password or
ask for it on our support channel ``#com-square-support`` on Slack.


The explore tool
----------------


.. For better performance queries must be constrained by time.

.. Template variables dashboardLowerLimit and dashboardUpperLimit


Creating a new dashboard
------------------------

We recommend following the Chronograf documentation for `creating a new dashboard`_

.. _creating a new dashboard: https://docs.influxdata.com/chronograf/v1.10/guides/create-a-dashboard/#build-a-dashboard


In addition to that, this section provides some extra tips that we learned by using Chronograf in the context of Rubin.


Visualization types
^^^^^^^^^^^^^^^^^^^

See the `visualization types`_ avaibale in Chronograf.

.. _visualization types: https://docs.influxdata.com/chronograf/v1.10/guides/visualization-types/


Time series of physical variables like temperature, pressure, etc are correlated data points, in this case plot the data using use a line graph or a line graph + single stat.
The single stat always corresponds to the most recent value in the time series.

If the data points are uncorrelated (events) then a bar chart is a better option over a line chart.

The best way to identify the best visualization type is by questioning the data.
For example, bar charts are useful to visualize gaps in the data.


Adding multiple graphs to one chart
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Sometimes it is useful to display multiple time series in a single chart, each time-serie can be added as and indepented query in Chronograf.


Multiple charts and time axis alignment
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

-


Using dashboard template variables
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

When creating a dashboard, you can use either predefined template variables or custom template variables to parametrize your queries and visualizations.

https://docs.influxdata.com/chronograf/v1.10/guides/dashboard-template-variables/

Using Flux for creating more advanced dashboards
------------------------------------------------

-

Visualizing log events
----------------------

And easy way to visualize log messages from events and correlate them with a time series chart is by using a linked table.

Tables are linked with charts via the time column.


.. note::

   Chronograf provides a `log viewer`_ tool that could be used to visualize CSC log events.
   To use the log viewer tool in Chronograf, data needs to be recorded in a specific measusrement and follow the syslog data format.
   In DM-31618 we explore this possibility.


.. _log viewer: https://docs.influxdata.com/chronograf/v1.10/guides/analyzing-logs/



Adding annotations
------------------

.. note::

   The log messages in every event topic could be automatically added as annotations (markers) on the time series.
   That would be a nice way to correlate events and telemetry and would be very useful for debugging.
   The `annotator` service would consume the event topics and write them as annotations in Chronograf, and a tag system would allow to filter annotatoins by CSC or event type, for example.

Extras
------

`Presentation mode`_ allows you to view Chronograf dashboards in full screen.

.. _Presentation mode: https://docs.influxdata.com/chronograf/v1.10/guides/presentation-mode/


Known limitations
-----------------

- There's solution yet to display units in Chronograf charts other than manually adding a suffix to the y-axis value.
  Units could be obtained from the topic schema, currently they can be inspected using the EFD client only.

- When adding multiple graphs to one chart, it is not possible to combine different visualization types.


Creating alert rules
--------------------


.. _Kapacitor: https://docs.influxdata.com/kapacitor



Monitoring CSC heartbeats
-------------------------



EFD data replication
====================

:sqr:`050` :cite:`SQR-050` describes the EFD data replication from Summit/TTS to USDF.
MirrorMaker 2 is the component responsible for that.

In the EFD setup, the MirrorMaker 2 source connectors run at the USDF and consume remote Kafka topics at the Summit and TTS.

Avro schemas recorded in the `registry-schemas` topic are also replicated.

If the InfluxDB instance at the Summit falls over, the InfluxDB instance at the USDF can still be used to access the EFD data provided that the replication service is running.
Note, however, that Chronograf dashboards and Kapacitor alert rules are not part of the this replication service.



References
==========

.. Make in-text citations with: :cite:`bibkey`.

.. bibliography:: local.bib lsstbib/books.bib lsstbib/lsst.bib lsstbib/lsst-dm.bib lsstbib/refs.bib lsstbib/refs_ads.bib
  :style: lsst_aa

