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

Endpoints for EFD deployments
=============================

The following sections list the EFD instances available, where they run and who the intended audience is for each one.

The main entry point to the EFD is `Chronograf <https://docs.influxdata.com/chronograf/v1.8/>`_, where users can make queries and create dashboards against the EFD.


.. note::

  To login to Chronograf, you need to be a member of the GitHub lsst-sqre organization. If you are not, please drop a line on the ``#com-square`` LSSTC Slack channel.


See documentation for the `EFD Client <https://efd-client.lsst.io/>`_ if you plan to access the data from a notebook.

The Kafka Schema Registry and the Kafka Broker are used to configure the SAL Kafka producers to send data to an EFD instance.

Summit EFD
----------
Instance running at the Summit (Chile).

Intended audience: Commissioning and Science Verification teams.

Data at the Summit EFD is also replicated to the LDF EFD to enable project wide access.

Chronograf: https://chronograf-summit-efd.lsst.codes

InfluxDB HTTP API: https://influxdb-summit-efd.lsst.codes

Kafka Schema Registry: https://schema-registry-summit-efd.lsst.codes

Kafka Broker: kafka-0-summit-efd.lsst.codes:31090

Base EFD
--------

Instance running at the Base facility (Chile).

Intended audience: Commissioning and Science Verification teams.

The plan is to have replication from the summit to the base and from the base to LDF working soon.

Chronograf: https://chronograf-base-efd.lsst.codes

InfluxDB HTTP API: https://influxdb-base-efd.lsst.codes

Kafka Schema Registry: https://schema-registry-base-efd.lsst.codes

Kafka Broker: cp-helm-charts-cp-kafka-headless.cp-helm-charts:9092

LSP Integration EFD
--------------------

Instance running at NCSA on the LSP development cluster.

.. note::

  As of March 20, this instance holds a copy of the Summit EFD data and dashboards and can be used during the shutdown of the Rubin Observatory caused by the COVID-19 outbreak.

Intended audience: Commissioning and Science Verification teams.

Chronograf: https://lsst-chronograf-int-efd.ncsa.illinois.edu

InfluxDB HTTP API: https://lsst-influxdb-int-efd.ncsa.illinois.edu

Kafka Schema Registry: https://lsst-schema-registry-int-efd.ncsa.illinois.edu

Kafka Broker:  cp-helm-charts-cp-kafka-headless.cp-helm-charts:9092

LSP Stable EFD
---------------

Instance running at NCSA on the LSP production cluster.

Intended audience: Everyone in the project.

Chronograf: https://lsst-chronograf-efd.ncsa.illinois.edu

InfluxDB HTTP API: https://lsst-influxdb-efd.ncsa.illinois.edu

Kafka Schema Registry: https://lsst-schema-registry-efd.ncsa.illinois.edu

Kafka Broker: cp-helm-charts-cp-kafka-headless.cp-helm-charts:9092

Tucson test stand EFD
---------------------

Standalone instance running at the Tucson test stand.

Intended audience: Telescope and Site Team.

Chronograf: https://chronograf-tucson-teststand-efd.lsst.codes

InfluxDB HTTP API: https://influxdb-tucson-teststand-efd.lsst.codes

Kafka Schema Registry: https://schema-registry-tucson-teststand-efd.lsst.codes

Kafka Broker: kafka-0-tucson-teststand-efd.lsst.codes:31090

NCSA test stand EFD
-------------------

Standalone instance running at the NCSA test stand.

Intended audience: Telescope and Site Team

Chronograf: https://lsst-chronograf-nts-efd.ncsa.illinois.edu

InfluxDB HTTP API: https://lsst-influxdb-nts-efd.ncsa.illinois.edu

Kafka Schema Registry: https://lsst-schema-registry-nts-efd.ncsa.illinois.edu

Kafka Broker: lsst-kafka-0-nts-efd.ncsa.illinois.edu:31090


Sandbox EFD at Google Cloud Platform
------------------------------------

Standalone instance running at GCP for sandbox activities.

Intended audience: Any group interested in having a testing environment.

Chronograf: https://chronograf-sandbox-efd.lsst.codes

InfluxDB HTTP API: https://influxdb-sandbox-efd.lsst.codes

Schema Registry: https://schema-registry-sandbox-efd.lsst.codes

Kafka Broker: cp-helm-charts-cp-kafka-headless.cp-helm-charts:9092


Follow ``#com-square`` on the LSSTC Slack for updates.



Introduction
============
In :dmtn:`082` :cite:`DMTN-082`, we present a high level architecture to enable real-time analysis of the Engineering Facilities Database (EFD) data in the LSST Science Platform (LSP).

In :sqr:`029` :cite:`SQR-029`, we describe the implementation of the EFD based on `Kafka`_  and `InfluxDB`_.  We report results of live tests with the LSST T&S `Service Abstraction Layer`_ (SAL) including latency characterization and performance evaluation with high-frequency telemetry.

Finally, in :sqr:`031` :cite:`SQR-031`, we describe the Kubernetes-based deployment of the EFD using Kubes (k3s), a lightweight Kubernetes, allowing us to use the EFD in non-production enviroments including single node deployments: e.g. test stand environments.

In this technote, we describe the EFD operation with 1) an instance at the Summit to store the data and to enable real-time analysis for observers, and 2) an instance at the LDF that replicates the data from the Summit and stores it for the long term. The EFD at the LDF is meant to be a centralized place where LSST staff can connect and perform their analysis without interfering with the Summit instance. One of the benefits of the present architecture is that it makes EFD data available at the LDF with latency under 1 second.

.. figure:: /_static/efd_architecture.png
   :name: Data flow from the Summit to the LDF.
   :target: _static/efd_architecture.png

   Data flow from the Summit to the LDF.

The main components of the EFD at the Summit are Kafka, InfluxDB, the InfluxDB Sink connector, Chronograf and Kapacitor. Currently, the SAL Kafka producers are not deployed as part of the EFD and are managed by the Telescope and Site team.
At the LDF, we also have the replicator, the aggregator and the connectors to write data to Parquet files and to a traditional RDBMS.
As seen from the endpoints listed in the first section, there are more deployments than simply the Summit and the LDF for various reasons.
However, those two deployments are the minimum we need to satisfy our requirements.

Also, we describe new components added to the EFD architecture, in particular, we discuss data replication, retention policies, and options for long-term storage of the EFD data.

The SAL Kafka producer
======================

The `SAL Kafka`_ producers forward DDS messages from one or more SAL components to Kafka.  For each DDS topic, SAL Kafka introspects the OpenSplice IDL, creates the Avro schema and uploads it to the Kafka Schema Registry dynamically. The Kafka brokers cache the Avro serialized messages, and consumers use the Avro schemas created by SAL Kafka to deserialize them.

SAL Kafka was an important addition to the EFD architecture, it decouples the EFD from the SAL XML schemas and introduces Avro as the interface between the DDS middleware and Kafka.

The Kafka Connect manager
=========================

Another addition to the EFD architecture is the `Kafka Connect manager`_. The Kafka Connect manager is the component responsible for managing the Kafka Connect REST interface. It is used to deploy the different connectors to the EFD. For connectors that are not dynamic like the InfluxDB Sink and the JDBc Sink connectors, the Kafka Connect manager can automatically update the connector configuration when new topics are created in Kafka.


Data replication and fault tolerance
====================================

The EFD uses Kafka to replicate data from and Summit EFD (primary site) to the LDF EFD (secondary site). The `Kafka Connect Replicator source connector`_ is the component responsible for that. In the EFD setup, the Replicator source connector runs in one direction pulling topics from the primary sites to the secondary site.

New topics and schemas in the Summit EFD are automatically detected and replicated to the LDF EFD. As throughput increases, the Replicator automatically scales to accommodate the increased load. Replicating topics and schemas across primary and secondary sites further protects the EFD against data loss.

Consumers at the Summit only read data from the primary site and consumers at the LDF only read data from the secondary site, with the exception of the Replicator.  Within the Kafka cluster we have fault tolerance by replicating the Kafka topics across three brokers (the default set up). That's done by the SAL Kafka producer creating topics with a replication factor of three.

If the InfluxDB instance in one of the primary sites dies, the InfluxDB instance on the secondary site can be used to access the data. However, there's no failover mechanism that automatically connects a consumer to the secondary site.

In summary, the LDF EFD provides long-term storage and a live backup of the EFD data (see the folowing section for details).


.. _retention-policy:

Downsampling and data retention
===============================

The EFD writes thousands of topics with frequencies ranging from 1Hz to 100Hz. Querying the raw EFD data on large time windows can be resource intensive, especially at the primary sites with limited computing resources.

A natural solution is to downsample the raw data and store one or two versions of low-resolution data for extended periods. In InfluxDB, it is possible to configure multiple retention policies. For instance, at the primary sites we can have 1 week of raw data, 1 month of an intermediate resolution version of the data, and 1 year of a low resolution version of the data. The retention policy is such that data older than the retention period is automatically deleted. The result is a moving time window on the most recent data in each case. Downsampling is efficiently done inside InfluxDB using Flux tasks that can be scheduled during daytime if necessary to keep for interfering with nighttime activities.  Similar retention policies at the LDF can be configured so that we can query the data efficiently over extended periods.

Real-time analysis of the EFD data might include statistical models for anomaly detection and forecasting. For example, InfluxDB implements a `built-in multiplicative Holt-Winter's <https://www.influxdata.com/blog/how-to-use-influxdbs-holt-winters-function-for-predictions/>`_ function to generate predictions on time series data. At the Summit, if we store 1 week of raw EFD data, that's roughly 0.2% of the data collected over the 10-year survey. Whether that's sufficient to build a statistical model or not depends on the long term trends and seasonality of the time-series we are analyzing. An interesting possibility of the present EFD architecture is to build the statistical models from historical data at the LDF EFD and apply the models to the Summit EFD when configuring alarms.

.. _aggregator:

The Aggregator
==============

As proposed in :dmtn:`082` :cite:`DMTN-082`, the LSP users are expected to generally be interested in telemetry data at a frequency closer to the cadence of the observations. It proposes that "all telemetry topics sampled with a frequency higher than 1Hz are (1) downsampled at 1Hz and (2) aggregated to 1Hz using general statistics like ``min``, ``max``, ``mean``, ``median`` ``stdev``".  Commands and event topics should not be aggregated as they are typically low-frequency and can be read directly from the raw EFD data sources.

In addition, the aggregator resamples the telemetry topics in a regular time grid to make it easier to correlate them.

The aggregator stream-processor produces a new set of aggregated telemetry topics in Kafka that can be consumed and stored in Parquet, InfluxDB and in an RDBMS. That gives the user multiple options to combine the aggregated telemetry with the exposure table which resides in the LDF consolidated database:

* inside the LSP notebook environment using Pandas data-frames after querying the exposure table and reading the telemetry data from one of the sources above;

* inside the consolidated database joining the exposure and the telemetry tables using SQL;

* Inside InfluxDB using Flux ``sql.from()`` function to retrieve data from the exposure table.

All these "joins" are based on timestamps.

The Aggregator is implemented in `Faust`_, a Python asyncio stream processing library. Faust supports `Avro serialization <https://github.com/marcosschroh/faust-docker-compose-example#avro-schemas-custom-codecs-and-serializers>`_ and multiple instances of a Faust worker can be started independently to distribute stream processing across nodes or CPU cores.


Options for long-term storage at the LDF
========================================

The LSP benefits from accessing data stored in Parquet format.  Parquet is compatible with  `Dask`_, a library used to scale computations across multiple worker nodes. The Confluent Kafka connect storage-cloud connector recently added `support for Parquet on S3 <https://github.com/confluentinc/kafka-connect-storage-cloud/pull/241>`_. From the connector configuration, it is possible to partition data based on time. We might want to store both the raw EFD data and the aggregated EFD data in Parquet files.  This would serve as a cold backup of the full raw EFD data.

We plan on storing the aggregated EFD data in the LDF consolidated database, which is convenient to make joins with the exposure table as discussed in the :ref:`aggregator` session. The `Kafka Connect JDBC connector`_ supports connections to all popular RDBMS implementations. The JDBC Sink connector automatically creates the destination tables if the ``auto.create`` configuration option is enabled, and can also `perform limited auto-evolution <https://docs.confluent.io/current/connect/kafka-connect-jdbc/sink-connector/index.html#auto-creation-and-auto-evoluton>`_ on the destination tables if the ``auto.evolve`` configuration option is enabled.  An alternative, is to load data to the consolidated database from Parquet files in batch.  The trade off is that we lose the convenience of creating and evolving the database schema offered by the JDBC Sink connector.

We can store the raw data for more extended periods at the LDF than in the Summit simply due to the fact that storage and processing is concentrated at the LDF.  We will tune multiple retention policies in InfluxDB and store low-resolution versions of the data at the Summit to allow for access to longer time windows as discussed in the :ref:`retention-policy` session.


Monitoring
==========

For monitoring the Kafka cluster, we use Prometheus deployed with the Confluent Kafka Helm charts.  There are multiple open source Kafka monitoring systems.  Evaluation is ongoing.  For InfluxDB, we collect system metrics from a different number of Telegraf plugins. We will ingest the EFD logs in the logging infrastructure at tje Summit as well as the LDF.




Appendix A - Configuring the Kafka Connect Replicator source connector
======================================================================

We've added the `Kafka Connect Replicator source connector`_ version 5.3.1 to our `Kafka Connect container image <https://github.com/lsst-sqre/kafka-efd-demo/blob/master/k8s-cluster/cp-kafka-connect/Dockerfile>`_ and tested topic replication and schema migration.

In this setup, the `topic replication <https://docs.confluent.io/current/multi-dc-replicator/index.html#multi-dc>`_ works in one direction. The Replicator source connector consumes topics from the source cluster and the Kafka Connect workers produce topics to the destination cluster. Replicated topics are namespaced to indicate their origin. For example, ``summit.{topic}`` indicates that the topic is replicated from the Summit EFD, etc.

Schema migration follows the `continuous migration <https://docs.confluent.io/current/schema-registry/installation/migrate.html#schemaregistry-migrate>`_ model. The Replicator continuously copy schemas from the source cluster to the destination cluster Schema Registry, which is set to IMPORT mode. `Schema translation <https://docs.confluent.io/current/tutorials/examples/replicator-schema-translation/docs/index.html>`_ ensures that subjects are renamed following the topic rename strategy when migrated to the destination Schema Registry.

An example of configuration for the Replicator that includes topic and schema replication with schema translation can be found `here <https://github.com/lsst-sqre/kafka-efd-demo/blob/master/k8s-cluster/cp-kafka-connect/make_replicator_config.sh>`_.


.. figure:: /_static/replicator_connector.png
   :name: Set up for testing the replicator connector.
   :target: _static/replicator_connector.png

Note that Kafka Connect ``bootstrap.servers`` configuration must include the URL of the destination Kafka cluster and that the destination Schema Registry must be in IMPORT mode. To initialize the destination Schema Registry to IMPORT mode, first set ``mode.mutability=True`` in the configuration and make sure the destination Schema Registry is empty. See `schema migration configuration <https://docs.confluent.io/current/schema-registry/installation/migrate.html#id1>`_ with the Replicator connector for details.

Confluent's recommendation is to deploy the Replicator source connector at the destination cluster (remote consuming). However, in our current set up some EFD deployments are not visible from the destination cluster due to VPNs and other networking considerations.  Thus, we have deployed the Replicator source connector at the source clusters (remote producing). We have tested the this set up to replicate data from the Summit EFD and Tucson test stand EFD to our EFD instance running on Google Cloud.  Another good practice is to have a separate Kafka Connect deployment for the Replicator source connector, to isolate this connector from other connectors running in the cluster.


References
==========

.. Make in-text citations with: :cite:`bibkey`.

.. bibliography:: local.bib lsstbib/books.bib lsstbib/lsst.bib lsstbib/lsst-dm.bib lsstbib/refs.bib lsstbib/refs_ads.bib
  :style: lsst_aa


.. _InfluxDB: https://www.influxdata.com/
.. _Kafka: https://www.confluent.io/
.. _Service Abstraction Layer: https://docushare.lsstcorp.org/docushare/dsweb/Get/Document-21527
.. _SAL Kafka: https://ts-salkafka.lsst.io/
.. _Kafka Connect manager: https://kafka-connect-manager.lsst.io/
.. _Faust: https://faust.readthedocs.io/en/latest/index.html
.. _Dask: https://dask.org/
.. _Kafka Connect JDBC connector: https://www.confluent.io/hub/confluentinc/kafka-connect-jdbc
.. _Kafka Connect Replicator source connector: https://www.confluent.io/hub/confluentinc/kafka-connect-replicator
.. _InfluxData stack: https://docs.influxdata.com/influxdb/v1.7/
.. _Chronograf: https://docs.influxdata.com/chronograf/v1.7/
.. _Kapacitor: https://docs.influxdata.com/kapacitor/v1.5/
