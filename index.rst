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

.. note::

   **This technote is not yet published.**

   Describe the EFD data flow, retention policies, etc

Introduction
============
:dmtn:`082` :cite:`DMTN-082` presents a proposal to enable real-time analysis of the EFD data in the LSST Science Platform (LSP). :sqr:`029` :cite:`SQR-029` describes a prototype implementation of the EFD based on the `Confluent Kafka Platform`_  and the `InfluxData stack`_ and reports results of live tests with the LSST T&S `Service Abstraction Layer`_ (SAL) including latency characterization and performance evaluation with high-frequency telemetry. :sqr:`031` :cite:`SQR-031` describes the EFD Kubernetes-based deployment using Kubes (k3s), a lightweight Kubernetes, that allowed us to exercise the deployment and operation of the EFD at the Auxiliary Telescope (AT) test stand in Tucson and at the Summit in Chile while we implement the final on-premise deployment platform.

In this technote, we describe new components added to the EFD architecture, data replication from the Summit to the LSST Data Facility (LDF), data storage options for long-term analyses of the EFD data in the LSP, and the EFD data retention policy.


.. figure:: /_static/efd_data_flow.png
   :name: EFD data flow
   :target: _static/efd_data_flow.png

   EFD components at the Summit and LDF.


The SAL Kafka producer
======================

The `SAL Kafka`_ producer runs at the Summit and forward DDS messages from one or more SAL components to the Kafka brokers.  For each DDS topic, SAL Kafka introspects the OpenSplice IDL, creates the Avro schema and upload it to the Kafka Schema registry dynamically. The Kafka brokers cache the Avro serialized messages, and consumers use the Avro schemas created by SAL Kafka to deserialize the messages and write them to the different destinations.

SAL Kafka was an important addition to the EFD architecture, it decoupled the EFD from the SAL XML schemas and introduced Avro as the interface between the DDS middleware and Kafka.

The Kafka Connect manager
=========================

Another addition to the EFD architecture was the `Kafka Connect manager`_. The Kafka Connect manager is the component responsible for managing the Kafka Connect REST interface. It is used to deploy the different connectors in the EFD and can automatically update the connector configuration when new topics are created in the Kafka broker.


Data replication and fault tolerance
====================================

The EFD uses Kafka to replicate data from the Summit (primary site) to the LDF (secondary site). The `Confluent Replicator`_ connector is the component responsible for that. In the EFD setup, the Replicator connector runs in one direction copying data from the primary site to the secondary site.

New topics in the Kafka brokers at the Summit are automatically detected and replicated to the Kafka brokers at the LDF. As throughput increases, the Replicator automatically scales to accommodate the increased load. The Replicator also synchronizes the Schema registry across the two sites, which further protects the EFD against data loss.

Note that in the present setup we have an InfluxDB consumer that reads data from the Kafka cluster at the primary site, and the Aggregator, InfluxDB, Oracle and Parquet consumers that read data from the Kafka cluster at the secondary site. Within the Kafka cluster we have fault tolerance by replicating the Kafka topics across the brokers. That's done by SAL Kafka producer creating topics with a replication factor of 3 by default. If one of the InfluxDB instances die, we still can connect to the other InfluxDB instance for real-time analysis of the EFD data. However, note that there's no failover mechanism for a consumer in the primary (secondary) site to read data from the secondary (primary) site. Similarly, the SAL Kafka producer writes data to just the primary site.

In summary, the data replication enables long-term storage of the EFD data at the LDF and also provides a live backup of the EFD data in the Summit instance (see :ref:`retention-policy`).


.. _retention-policy:
Downsampling and data retention
===============================

The EFD writes thousands of topics with frequencies ranging from 1Hz to 100Hz. Querying the raw EFD data on large time windows can be quite painful, especially at the Summit with limited computing resources.

A natural solution is to downsample the raw data and store one or two versions of low-resolution data for extended periods. In InfluxDB, it is possible to configure multiple retention policies. For instance, at the Summit we can have 1 week of raw data, 1 month of an intermediate resolution version, and 1 year of a low resolution version. The retention policy is such that data older than the retention period is automatically deleted. The result is a moving time window on the most recent data in each case. Downsampling is efficiently done inside InfluxDB using Flux tasks that can be scheduled during daytime.  Similar retention policies at the LDF can be configure so that we can query the data efficiently over extended periods in InlfuxDB.

Real-time analysis of the EFD data might include statistical models for anomaly detection and forecasting. For example, InfluxDB implements a `built-in multiplicative Holt-Winter's <https://www.influxdata.com/blog/how-to-use-influxdbs-holt-winters-function-for-predictions/>`_ function to generate predictions on time series data. At the Summit, if we store 1 month of raw EFD data, that's roughly 1% of the data collected over the 10-years survey. If that's sufficient to build a statistical model or not depends on the long term trends and seasonality of the time-series we are analyzing. An interesting possibility of the present EFD architecture is to build the statistical models at the LDF where we have the raw data stored for longer periods and apply the models at the Summit when configuring alerts.


The Aggregator
==============

Long-term storage at the LDF
============================


References
==========

.. Make in-text citations with: :cite:`bibkey`.

.. bibliography:: local.bib lsstbib/books.bib lsstbib/lsst.bib lsstbib/lsst-dm.bib lsstbib/refs.bib lsstbib/refs_ads.bib
   :style: lsst_aa


.. _InfluxData: https://www.influxdata.com/
.. _Confluent Kafka Platform: https://www.confluent.io/
.. _Service Abstraction Layer: https://docushare.lsstcorp.org/docushare/dsweb/Get/Document-21527
.. _SAL Kafka: https://ts-salkafka.lsst.io/
.. _Kafka Connect manager: https://kafka-connect-manager.lsst.io/
.. _Confluent Replicator:
.. _InfluxData stack: https://docs.influxdata.com/influxdb/v1.7/
.. _Chronograf: https://docs.influxdata.com/chronograf/v1.7/
.. _Kapacitor: https://docs.influxdata.com/kapacitor/v1.5/
