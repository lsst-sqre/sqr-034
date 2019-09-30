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

In this technote, we'll detail the EFD operations based on what we've learned so far operating the EFD for the AT activities. We'll discuss the EFD data flow from the Summit to the LSST Data Facility (LDF), data retention policies, data formats and storage options for both real-time and long-term analyses of the EFD data in the LSP.

EFD data flow
=============

This section describes new components of the EFD architecture, the SAL Kafka producer, the Kafka Connect manager, new Kafka connectors that enable data replication from the Summit to the LDF, and the Aggregator to downsample the data stream for long-term storage at the LDF.


.. figure:: /_static/efd_data_flow.png
   :name: EFD data flow
   :target: _static/efd_data_flow.png

   EFD components and data flow from the Summit to the LDF.

The SAL Kafka producer
----------------------

The `SAL Kafka`_ producer is the service that forwards DDS messages from one or more SAL components to the Kafka brokers. Its addition was essential to the EFD architecture decoupling the EFD from the SAL XML schemas and using Avro schemas as the interface between the DDS middleware and Kafka. For each DDS topic, `SAL Kafka`_ introspects the OpenSplice IDL, creates the Avro schema and upload it to the Kafka Schema registry dynamically. The Kafka brokers cache the Avro serialized messages, and the Kafka connectors use the same Avro schemas to deserialize and write them to the different destinations.


The Kafka Connect manager
-------------------------

Another addition to the EFD architecture was the `Kafka Connect manager`_. The Kafka Connect manager is the component responsible for managing the Kafka Connect REST interface, it simplifies the connector creation and automatically updates the connector configuration every time the `SAL Kafka`_ producer creates a new topic in Kafka.

Data replication
----------------


Data aggregation
----------------


Data formats
------------


EFD data retention policy
=========================

The EFD data retention policy defines the period in which the data is retained in each component of the EFD. It also provides guidelines for sizing the hardware to store and query the data.

At the Summit, we configure the Kafka brokers with the default retention period of one week. It means that if the data cannot be written to InfluxDB or replicated to the LDF, they are still available on the Kafka brokers for this period before getting discarded. We configure InfluxDB with a retention period of one month. Data older than one month do not seem useful for analysis at the Summit.

At LDF, we also configure the Kafka brokers with the default retention period of one week. The retention period for InfluxDB, Oracle, and Parquet files is infinite, which means it preserves the data for the lifetime of the experiment.

The replication of the EFD data to the LFD provides a live backup of the Summit instance. In the same time, the replication of the EFD data to Parquet files provides a backup of both the raw and aggregated streams.


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
.. _InfluxData stack: https://docs.influxdata.com/influxdb/v1.7/
.. _Chronograf: https://docs.influxdata.com/chronograf/v1.7/
.. _Kapacitor: https://docs.influxdata.com/kapacitor/v1.5/
