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

Quick access to EFD data.

The main entry points are Chronograf or the EFD client from where users can query the EFD.

+-------------------------+-----------------------------------------------------------+--------------------------+
| **Sasquatch Instance**  | **Chronograf UI**                                         | **EFD  Client Key**      |
+=========================+===========================================================+==========================+
| Summit                  | https://summit-lsp.lsst.codes/chronograf                  | ``summit_efd``           |
+-------------------------+-----------------------------------------------------------+--------------------------+
| USDF                    | https://usdf-rsp.slac.stanford.edu/chronograf             | ``usdf_efd``             |
+-------------------------+-----------------------------------------------------------+--------------------------+
| Tucson Test Stand (TTS) | https://tucson-teststand.lsst.codes/chronograf            | ``tucson_teststand_efd`` |
+-------------------------+-----------------------------------------------------------+--------------------------+
| Base Test Stand (BTS)   | https://chronograf-base-efd.lsst.codes                    | ``base_efd``             |
+-------------------------+-----------------------------------------------------------+--------------------------+

.. note::

  If you need help, please drop a line on the ``#com-square-support`` LSSTC Slack channel.


Chronograf
----------

Chronograf is the UI for creating dashboards and exploring time-series data.

Learn more from the `Chronograf documentation`_.

.. _Chronograf documentation: https://docs.influxdata.com/chronograf/v1.9

EFD Client
----------

EFD client is a Python client to access EFD data from the RSP notebook aspect.

Instantiate the EFD client using:

.. code::

   from lsst_efd_client import EfdClient
   efd = EfdClient('usdf_efd')

   await efd.get_topics()

Learn more about the helper methods implemented from the `EFD client documentation`_.

.. _EFD client documentation: https://efd-client.lsst.io


Sasquatch Instances
===================

In this section we describe the Sasquatch instances that host the EFD.

In addition to Chronograf and the EFD client, other services available include the InfluxDB HTTP API, Schema Registry, Kafka broker, and the Kafdrop UI.


Summit
------

Instance running at the Summit (Chile). The Summit EFD data is also replicated to the USDF instance for project wide access.

Intended audience: Observers and Commissioning team.

- Chronograf: ``https://summit-lsp.lsst.codes/chronograf``
- InfluxDB HTTP API: ``https://summit-lsp.lsst.codes/influxdb``
- Schema Registry: ``http://sasquatch-schema-registry.sasquatch:8081``
- Kafka Broker: ``sasquatch-kafka-brokers.sasquatch``
- Kafdrop UI: ``https://tucson-teststand.lsst.codes/kafdrop``

.. note::

  When deploying the SAL Kafka producers on Kubernetes use the internal address for the Kafka broker: ``cp-helm-charts-cp-kafka-headless.cp-helm-charts:9092``.


USDF
----

Instance running at SLAC. It has a replica of the Summit EFD data.

Intended audience: Project staff.

- Chronograf: ``https://usdf-rsp.slac.stanford.edu/chronograf``
- InfluxDB HTTP API: ``https://usdf-rsp.slac.stanford.edu/influxdb``
- Confluent Schema Registry: ``http://sasquatch-schema-registry.sasquatch:8081``
- Kafka Broker: ``sasquatch-kafka-brokers.sasquatch``
- Kafdrop UI: ``https://usdf-rsp.slac.stanford.edu/kafdrop``


Tucson Test Stand (TTS)
-----------------------

Standalone instance running at the Tucson Test Stand.

Intended audience: Telescope & Site team.

- Chronograf: ``https://tucson-teststand.lsst.codes/chronograf``
- InfluxDB HTTP API: ``https://tucson-teststand.lsst.codes/influxdb``
- Schema Registry: ``http://sasquatch-schema-registry.sasquatch:8081``
- Kafka Broker: ``sasquatch-kafka-brokers.sasquatch``
- Kafdrop UI: ``https://tucson-teststand.lsst.codes/kafdrop``

.. _LDF EFD:


Base Test Stand (BTS)
---------------------

Standalone instance running at the Base facility (Chile) (Kueyen cluster).

Intended audience: Telescope & Site team.

- Chronograf: ``https://chronograf-base-efd.lsst.codes``
- InfluxDB HTTP API: ``https://influxdb-base-efd.lsst.codes``
- Confluent Schema Registry: ``https://schema-registry-base-efd.lsst.codes``
- Kafka Broker: ``cp-helm-charts-cp-kafka-headless.cp-helm-charts:9092``
- Kafdrop UI: ``https://kafdrop-base-efd.lsst.codes``


Introduction
============

In :dmtn:`082` :cite:`DMTN-082`, we present the high level architecture to enable real-time analysis of the Engineering and Facilities Database (EFD) data from the Rubin Science Platform (RSP).

In :sqr:`029` :cite:`SQR-029`, we describe a prototype implementation of the EFD based on `Kafka`_  and `InfluxDB`_.
We also report results of live tests with the `Service Abstraction Layer`_ (SAL) including latency characterization and performance evaluation with high-frequency telemetry.

In this technote, we describe the EFD operation with an instance at the Summit, to store the data and to enable real-time analysis during observations, and an instance at the LSST Data Facility (LDF) to replicate Summit EFD data.

The LDF EFD is meant to be a centralized place where Rubin Observatory staff can connect and perform their analysis without interfering with the Summit EFD which is meant to be used by the observers.

The current architecture based on Kafka and InfluxDB.

.. figure:: /_static/efd_summit.svg
   :name: EFD components for the Summit or TestStand instance.
   :target: _static/efd_summit.svg

   EFD components for the Summit or TestStand instance.

A TestStand deployment has the same components of the Summit deployment: Kafka, InfluxDB, InfluxDB Sink connector, Chronograf and Kapacitor. SAL Kafka producers are managed by the T&S team.

For the LDF EFD, we have in addition the MirrorMaker 2 connectors for the EFD replication service, the Aggregator, and other connectors to write data to Parquet files and to the Consolidated Database (PostgreSQL).

.. figure:: /_static/efd_ldf.svg
   :name: LDF EFD components.
   :target: _static/efd_ldf.svg

   LDF EFD components.

In the following sections we describe the new components added to the EFD architecture, we discuss data replication, retention policies, and options for long-term storage of the EFD data.

.. _Service Abstraction Layer: https://docushare.lsstcorp.org/docushare/dsweb/Get/Document-21527
.. _Kafka: https://www.confluent.io/
.. _InfluxDB: https://www.influxdata.com/


SAL Kafka producer
==================

The `SAL Kafka`_ producers forward DDS messages from one or more SAL components to Kafka.
For each DDS topic, SAL Kafka introspects the OpenSplice IDL, creates the Avro schema and uploads it to the Kafka Schema Registry dynamically.
The Kafka brokers store the Avro messages, and consumers use the Avro schemas to deserialize them.

SAL Kafka was an important addition to the EFD architecture, it decouples the EFD from the SAL XML schemas and introduces Avro as the interface between the DDS middleware and the EFD.

.. _SAL Kafka: https://ts-salkafka.lsst.io/


Kafka Connect manager
=====================

Another addition to the EFD architecture is the `Kafka Connect manager`_.
The Kafka Connect manager is the component responsible for managing the Kafka Connect REST interface.
It is used to deploy the different connectors to the EFD.
For connectors that are not dynamic like the InfluxDB Sink and the JDBc Sink connectors, the Kafka Connect manager can automatically update the connector configuration when new topics are created in Kafka.

.. _Kafka Connect manager: https://kafka-connect-manager.lsst.io

The EFD replication service
===========================

:sqr:`050` :cite:`SQR-050` describes the EFD replication service. MirrorMaker 2 is the component responsible for that. In the EFD setup, the MirrorMaker 2 connectors run on the LDF EFD and pull Kafka records and topic configuration from the Summit EFD.

New topics and schemas at the Summit EFD are automatically detected and replicated to the LDF EFD.
As throughput increases, we can add more partitions to the Kafka topics and the connector, running on the Kafka Connect Framework, can scale up to accommodate the increased load.
Replicating topics and schemas across the two sites also protects the EFD against data loss.

Chronograf dashboards and Kapacitor alert rules are not yet part of the replication service.

Consumers at the Summit only read data from the Summit and consumers at the LDF only read data from the LDF, with the exception of the Mirror Maker 2 mirror source connector.
Within the Kafka cluster, we have fault tolerance by replicating the Kafka topics across three brokers (default set up).

If the InfluxDB instance at the Summit falls over, the InfluxDB instance at the LDF can still be used to access the replicated data.
However, there is no automatic mechanism to connect to the LDF EFD.

Data replication enables unrestricted access to EFD data from the LDF.
It also provides long-term storage and a live backup of the Summit EFD data.

.. _replicate data from and Summit EFD to the LDF EFD: https://sqr-050.lsst.io

.. _retention-policy:

Downsampling and data retention
===============================

The EFD writes thousands of topics on a wide range of frequencies. Querying the EFD raw data on large time windows can be resource intensive.

A natural solution is to downsample the raw data and store one or two versions of lower resolution data for extended periods.
In InfluxDB, it is possible to configure multiple retention policies.
For instance, at the Summit we can have one month of raw data, three months of intermediate resolution data, and perhaps one year of low resolution data.
When the retention policy is enforced, data older than the retention period is automatically deleted.
The result is a moving window on the most recent data.

Downsampling is efficiently done inside InfluxDB using Flux tasks that can be scheduled during daytime if necessary to not interfere with the observations.
These extra retention policies are not replicated to the LDF EFD but similar retention policies can be configured there as well to query the data efficiently over extended periods.

Real-time analysis of the EFD data could potentially include statistical models for anomaly detection and forecasting.
For example, InfluxDB implements a `built-in multiplicative Holt-Winters`_  function to generate predictions on time series data.

At the Summit, if we store one month of raw data, that's roughly 0.8% of the data collected over the 10-year survey.
Whether that's sufficient to build a statistical model or not it depends on the long term trends and seasonality of the time-series we are analyzing.
An interesting possibility of the EFD architecture is to build the statistical models from historical data at LDF and apply these models to the Summit.


.. _built-in multiplicative Holt-Winters: https://www.influxdata.com/blog/how-to-use-influxdbs-holt-winters-function-for-predictions


.. _aggregator:

The EFD transformation service
==============================

As proposed in :dmtn:`082` :cite:`DMTN-082`, RSP users are expected to generally access telemetry data at a frequency closer to the cadence of the observations.
It proposes that "all telemetry topics sampled with a frequency higher than 1Hz are (1) downsampled at 1Hz and (2) aggregated to 1Hz using  ``min``, ``max``, ``mean``, ``median`` ``stdev`` statistics".
Commands and event topics should not be aggregated as they are typically low-frequency and can be read directly from the raw EFD data sources.

:sqr:`058` :cite:`SQR-058` describes the EFD transformation service. It uses the Aggregator to produce a new set of aggregated telemetry topics in Kafka that can be consumed by the different connectors and stored in different formats (Parquet, InfluxDB and PostgreSQL).

.. figure:: /_static/kafka-aggregator.svg
   :name: Kafka Aggregator
   :target: _static/kafka-aggregator.svg

   Kafka Aggregator based on the Faust stream processing library.


The `Kafka Aggregator <https://kafka-aggregator.lsst.io/>`_ is implemented in `Faust`_, a Python stream processing library. Faust supports `Avro serialization <https://github.com/marcosschroh/faust-docker-compose-example#avro-schemas-custom-codecs-and-serializers>`_ and multiple instances of a Faust worker can be started independently to distribute stream processing across nodes or CPU cores.


.. _Faust: https://faust.readthedocs.io/en/latest/index.html

Options for long-term storage
=============================

In the RSP we can access EFD data from InfluxDB directly using the EFD client or from data stored in Parquet files.
Parquet is compatible with  `Dask`_, a library used to scale computations across multiple worker nodes.
The Confluent Amazon S3 Sink connector `supports Parquet on S3`_.
From the connector configuration, it is possible to partition data based on time. We might want to store both the raw EFD data and the aggregated EFD data in Parquet files.
This would serve as a live backup of the full raw EFD data.

We plan on storing the aggregated EFD data in the LDF consolidated database, which is convenient to make joins with the exposure table as discussed in session :ref:`aggregator`. The `Kafka Connect JDBC connector`_ supports connections to several RDBMS implementations.

We can store the raw data for more extended periods at the LDF than in the Summit.
We plan on tuning multiple retention policies in InfluxDB and store lower resolution versions of the data at the LDF and at the Summit, as discussed in session :ref:`retention-policy`.

.. _Dask: https://dask.org/
.. _Kafka Connect JDBC connector: https://www.confluent.io/hub/confluentinc/kafka-connect-jdbc
.. _supports Parquet on S3: <https://docs.confluent.io/current/connect/kafka-connect-s3/>

Monitoring
==========

For monitoring the Kafka cluster, we use the Kafdrop UI and also monitor JMX metrics exposed by the Confluent Platform.
JMX is a common technology in Java to export application metrics.
Confluent Kafka components use JMX APIs to collect application and JVM metrics and expose them over HTTP in a format that Prometheus understands and can scrape.
We then use the Telegraf input Prometheus plugin to write these metrics to InfluxDB and create a Kafka monitoring dashboard in Chronograf.

For monitoring InfluxDB itself, we collect system and InfluxDB metrics using Telegraf and create alert rules with `Kapacitor`_.

We plan on ingesting the EFD logs into the logging infrastructure at the Summit and IDF too.

.. _Kapacitor: https://docs.influxdata.com/kapacitor


References
==========

.. Make in-text citations with: :cite:`bibkey`.

.. bibliography:: local.bib lsstbib/books.bib lsstbib/lsst.bib lsstbib/lsst-dm.bib lsstbib/refs.bib lsstbib/refs_ads.bib
  :style: lsst_aa

