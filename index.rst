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

EFD deployments
===============

This section list the EFD instances available, where they run and who is the intended audience for each one.

The main entry point to the EFD is `Chronograf <https://docs.influxdata.com/chronograf/v1.8/>`_, where users can make queries and create dashboards against the EFD.

.. note::

  To login to Chronograf, you need to be a member of the GitHub lsst-sqre or lsst-ts organizations. If you are not, please drop a line on the ``#com-square`` LSSTC Slack channel.


The Kafka Broker and the Confluent Schema Registry services are used to configure the SAL Kafka producers to send data to an EFD instance.

.. note::

  When the SAL Kafka producers are deployed on Kubernetes we use the internal address for the broker: ``cp-helm-charts-cp-kafka-headless.cp-helm-charts:9092``.

To access EFD data from a notebook, see documentation on using the `EFD Client <https://efd-client.lsst.io/>`_.


.. note::

  We plan on moving the NCSA TestStand EFD to the Base Facility and the NCSA Integration and Stable EFDs  to the Interim Data Facility (IDF). For operations in the IDF era, we'll have the Summit EFD replicating data to the IDF EFD and using the standalone Base and Tucson TestStands as testing environments.

Summit EFD
----------
Instance running at the Summit (Chile) on k3s ("kubes").

Intended audience: Commissioning and Science Verification teams.

Data at the Summit EFD is also replicated to the IDF EFD to enable project wide access.

- Chronograf: ``https://chronograf-summit-efd.lsst.codes``
- InfluxDB HTTP API: ``https://influxdb-summit-efd.lsst.codes``
- Confluent Schema Registry: ``https://schema-registry-summit-efd.lsst.codes``
- Kafka Broker: ``kafka-0-summit-efd.lsst.codes:31090``
- Kafdrop UI: ``https://kafdrop-summit-efd.lsst.codes``

Base TestStand EFD
------------------

Instance running at the Base facility (Chile) on the Kueyen k8s cluster. Was used for testing ComCam and will replace the NCSA TestStand in the future.

Intended audience: Commissioning and Science Verification teams.

- Chronograf: ``https://chronograf-base-efd.lsst.codes``
- InfluxDB HTTP API: ``https://influxdb-base-efd.lsst.codes``
- Confluent Schema Registry: ``https://schema-registry-base-efd.lsst.codes``
- Kafka Broker: ``cp-helm-charts-cp-kafka-headless.cp-helm-charts:9092``
- Kafdrop UI: ``https://kafdrop-base-efd.lsst.codes``


NCSA TestStand EFD
-------------------

Standalone instance running at the NCSA TestStand.

Intended audience: Telescope and Site Team

- Chronograf: ``https://lsst-chronograf-nts-efd.ncsa.illinois.edu``
- InfluxDB HTTP API: ``https://lsst-influxdb-nts-efd.ncsa.illinois.edu``
- Confluent Schema Registry: ``https://lsst-schema-registry-nts-efd.ncsa.illinois.edu``
- Kafka Broker: ``cp-helm-charts-cp-kafka-headless.cp-helm-charts:9092``
- Kafdrop UI: ``https://lsst-kafka-0-nts-efd.ncsa.illinois.edu``


NCSA Integration EFD
--------------------

Instance running at NCSA on the integration k8s cluster.

.. note::

  As of March 20, this instance holds a copy of the Summit EFD data and dashboards. It was used during the shutdown of the Rubin Observatory during the COVID-19 outbreak.

Intended audience: Commissioning and Science Verification teams.

- Chronograf: ``https://lsst-chronograf-int-efd.ncsa.illinois.edu``
- InfluxDB HTTP API: ``https://lsst-influxdb-int-efd.ncsa.illinois.edu``
- Confluent Schema Registry: ``https://lsst-schema-registry-int-efd.ncsa.illinois.edu``
- Kafka Broker: ``cp-helm-charts-cp-kafka-headless.cp-helm-charts:9092``


NCSA Stable EFD
---------------

Not running at the moment. It will be replaced by the EFD instance at the Interim Data Facility (IDF) at Google Cloud Platform. Compared to the a TestStand EFD this instance has additional components like the Replicator Source Connector and the Aggregator.

Intended audience: Everyone in the project.

- Chronograf: ``https://lsst-chronograf-efd.ncsa.illinois.edu``
- InfluxDB HTTP API: ``https://lsst-influxdb-efd.ncsa.illinois.edu``
- Confluent Schema Registry: ``https://lsst-schema-registry-efd.ncsa.illinois.edu``
- Kafka Broker: ``cp-helm-charts-cp-kafka-headless.cp-helm-charts:9092``


Tucson TestStand EFD
--------------------

Standalone instance running at the Tucson TestStand.

Intended audience: Telescope and Site Team.

- Chronograf: ``https://chronograf-tucson-teststand-efd.lsst.codes``
- InfluxDB HTTP API: ``https://influxdb-tucson-teststand-efd.lsst.codes``
- Confluent Schema Registry: ``https://schema-registry-tucson-teststand-efd.lsst.codes``
- Kafka Broker: ``kafka-0-tucson-teststand-efd.lsst.codes:31090``


Sandbox EFD
-----------

Standalone instance running at Google Cloud Platform for development.

Intended audience: Any group interested in having a development environment. For example this instance has been used by the T&S team to test SAL Kafka producer deployments.

- Chronograf: ``https://chronograf-sandbox-efd.lsst.codes``
- InfluxDB HTTP API: ``https://influxdb-sandbox-efd.lsst.codes``
- Confluent Schema Registry: ``https://schema-registry-sandbox-efd.lsst.codes``
- Kafka Broker: ``cp-helm-charts-cp-kafka-headless.cp-helm-charts:9092``
- Kafdrop UI: ``https://kafdrop-sandbox-efd.lsst.codes``


Follow ``#com-square`` on the LSSTC Slack for updates.


Introduction
============

In :dmtn:`082` :cite:`DMTN-082`, we present a high level architecture to enable real-time analysis of the Engineering and Facilities Database (EFD) data in the Rubin Science Platform (RSP).

In :sqr:`029` :cite:`SQR-029`, we describe a prototype implementation of the EFD based on `Kafka`_  and `InfluxDB`_.  We report results of live tests with the LSST T&S `Service Abstraction Layer`_ (SAL) including latency characterization and performance evaluation with high-frequency telemetry.

Finally, in :sqr:`031` :cite:`SQR-031`, we describe a Kubernetes-based deployment of the EFD using k3s ("kubes") a lightweight Kubernetes, allowing us to use the EFD in non-production environments including single node deployments: e.g. TestStand deployments where a proper k8s cluster was not yet available.

In this technote, we describe the EFD operation with 1) an instance at the Summit to store the data and to enable real-time analysis for observers, and 2) an instance at the Interim Data Facility (IDF) that replicates the data from the Summit and stores it for the long term. The EFD at the IDF is meant to be a centralized place where the Rubin staff can connect and perform their analysis without interfering with the Summit EFD which has restricted access to observers.

The current architecture based on Kafka and InfluxDB makes data available at the IDF with latency under 1 second.

.. figure:: /_static/efd_summit.svg
   :name: EFD components deployed at the Summit or TestStand.
   :target: _static/efd_summit.svg

   EFD components deployed at the Summit or TestStand.


As seen from the first section, in addition to the Summit and the IDF deployments there are the Base TestStand and the Tucson TestStand deployments. A TestStand deployment has the same components as the Summit deployment.

The main components for the Summit or for a TestStand deployment are Kafka, InfluxDB, the InfluxDB Sink connector, Chronograf and Kapacitor. The SAL Kafka producers are managed by the T&S team and are currently deployed on k8s in most of the environments now.

For the IDF EFD we have in addition the Replicator connector, the Aggregator and connectors to write data to Parquet files and to a relational database, e.g. Postgres.


.. figure:: /_static/efd_idf.svg
   :name: EFD components deployed at IDF.
   :target: _static/efd_idf.svg

   EFD components deployed at IDF.


In the following sections we describe the new components added to the EFD architecture, we discuss data replication, retention policies, and options for long-term storage of the EFD data.

The SAL Kafka producer
======================

The `SAL Kafka`_ producers forward DDS messages from one or more SAL components to Kafka.  For each DDS topic, SAL Kafka introspects the OpenSplice IDL, creates the Avro schema and uploads it to the Kafka Schema Registry dynamically. The Kafka brokers cache the Avro serialized messages, and consumers use the Avro schemas created by SAL Kafka to deserialize them.

SAL Kafka was an important addition to the EFD architecture, it decouples the EFD from the SAL XML schemas and introduces Avro as the interface between the DDS middleware and the EFD.

The Kafka Connect manager
=========================

Another addition to the EFD architecture is the `Kafka Connect manager`_. The Kafka Connect manager is the component responsible for managing the Kafka Connect REST interface. It is used to deploy the different connectors to the EFD. For connectors that are not dynamic like the InfluxDB Sink and the JDBc Sink connectors, the Kafka Connect manager can automatically update the connector configuration when new topics are created in Kafka.


Data replication and fault tolerance
====================================

The EFD uses Kafka to replicate data from and Summit EFD to the IDF EFD. The `Kafka Connect Replicator source connector`_ is the component responsible for that. In the EFD setup, the Replicator source connector runs in one direction pulling topics from the Summit site to the IDF.

New topics and schemas in the Summit EFD are automatically detected and replicated to the IDF EFD. As throughput increases, the Replicator can automatically scale up to accommodate the increased load. Replicating topics and schemas across the two sites further protects the EFD against data loss.

Consumers at the Summit only read data from the Summit and consumers at the IDF only read data from the IDF, with the exception of the Replicator.  Within the Kafka cluster we have fault tolerance by replicating the Kafka topics across the brokers (the default set up). That's done by the SAL Kafka producer creating topics with a number of replicas equal to the number of brokers.

If the InfluxDB instance at the Summit falls over, the InfluxDB instance at the IDF EFD can still be used to access the replicated data. However, there's no failover mechanism that automatically connects a consumer at the Summit to the IDF.

In summary, data replication enables unrestricted access to EFD data from the IDF. It also provides long-term storage and a live backup of the Summit EFD data.


.. _retention-policy:

Downsampling and data retention
===============================

The EFD writes thousands of topics with frequencies ranging from 1Hz to 100Hz. Querying the raw EFD data on large time windows can be resource intensive, especially at the primary sites with limited computing resources.

A natural solution is to downsample the raw data and store one or two versions of low-resolution data for extended periods. In InfluxDB, it is possible to configure multiple retention policies. For instance, at the Summit we can have 1 month of raw data, 3 months of intermediate resolution data, and perhaps 1 year of low resolution data.. The retention policy is such that data older than the retention period is automatically deleted. The result is a moving time window on the most recent data in each case. Downsampling is efficiently done inside InfluxDB using Flux tasks that can be scheduled during daytime if necessary to keep for interfering with nighttime activities. These extra retention policies are not replicated to the IDF EFD but similar retention policies can be configured at the IDF so that we can query the data efficiently over extended periods.

Real-time analysis of the EFD data could potentially include statistical models for anomaly detection and forecasting. For example, InfluxDB implements a `built-in multiplicative Holt-Winter's <https://www.influxdata.com/blog/how-to-use-influxdbs-holt-winters-function-for-predictions/>`_ function to generate predictions on time series data. At the Summit, if we store 1 month of raw data, that's roughly 0.8% of the data collected over the 10-year survey. Whether that's sufficient to build a statistical model or not depends on the long term trends and seasonality of the time-series we are analyzing. An interesting possibility of the present EFD architecture is to build the statistical models from historical data at the IDF EFD and apply these models to the Summit EFD when configuring alarms.

.. _aggregator:

The Aggregator
==============

As proposed in :dmtn:`082` :cite:`DMTN-082`, RSP users are expected to generally access telemetry data at a frequency closer to the cadence of the observations. It proposes that "all telemetry topics sampled with a frequency higher than 1Hz are (1) downsampled at 1Hz and (2) aggregated to 1Hz using general statistics like ``min``, ``max``, ``mean``, ``median`` ``stdev``".  Commands and event topics should not be aggregated as they are typically low-frequency and can be read directly from the raw EFD data sources.

In addition, the aggregator resamples the telemetry topics in a regular time grid to make it easier to correlate them.

The aggregator produces a new set of aggregated telemetry topics in Kafka that are consumed and stored in Parquet, InfluxDB and in an RDBMS. That gives the user multiple options to combine the aggregated telemetry with the exposure table which resides in the IDF consolidated database:

* inside the RSP notebook environment using Pandas data-frames after querying the exposure table and reading the telemetry data from Parquet files from an object store.

* inside the consolidated database by joining the exposure and the telemetry tables using SQL;

* Inside InfluxDB using Flux ``sql.from()`` function to retrieve data from the exposure table.

All these "joins" are based on timestamps.


.. figure:: /_static/kafka-aggregator.svg
   :name: Kafka Aggregator
   :target: _static/kafka-aggregator.svg

   Kafka Aggregator based on the Faust stream processing library.


The `Kafka Aggregator <https://kafka-aggregator.lsst.io/>`_ is implemented in `Faust`_, a Python stream processing library. Faust supports `Avro serialization <https://github.com/marcosschroh/faust-docker-compose-example#avro-schemas-custom-codecs-and-serializers>`_ and multiple instances of a Faust worker can be started independently to distribute stream processing across nodes or CPU cores.


Options for long-term storage
=============================

The RSP benefits from accessing data stored in Parquet format.  Parquet is compatible with  `Dask`_, a library used to scale computations across multiple worker nodes. The Confluent Amazon S3 Sink connector `supports Parquet on S3 <https://docs.confluent.io/current/connect/kafka-connect-s3/>`_. From the connector configuration, it is possible to partition data based on time. We might want to store both the raw EFD data and the aggregated EFD data in Parquet files.  This would serve as a live backup of the full raw EFD data.

We plan on storing the aggregated EFD data in the IDF consolidated database, which is convenient to make joins with the exposure table as discussed in the :ref:`aggregator` session. The `Kafka Connect JDBC connector`_ supports connections to all popular RDBMS implementations. The JDBC Sink connector automatically creates the destination tables if the ``auto.create`` configuration option is enabled, and can also `perform limited auto-evolution <https://docs.confluent.io/current/connect/kafka-connect-jdbc/sink-connector/index.html#auto-creation-and-auto-evoluton>`_ on the destination tables if the ``auto.evolve`` configuration option is enabled.  An alternative, is to load data to the consolidated database from Parquet files in batch.  The trade off is that we lose the convenience of creating and evolving the database schema offered by the JDBC Sink connector.

We can store the raw data for more extended periods at the IDF than in the Summit simply due to the fact that storage and processing is concentrated at the IDF.  We will tune multiple retention policies in InfluxDB and store low-resolution versions of the data at the IDF and at the Summit, as discussed in the :ref:`retention-policy` session.


Monitoring
==========

For monitoring the Kafka cluster, we use the Kafdrop UI and also monitor JMX metrics exposed by the Confluent Platform.  JMX is a common technology in Java world for exporting application metrics. Confluent Kafka components use JMX APIs to collect application and JVM metrics and expose them over HTTP in a format that Prometheus understands and can scrape. We then use the Telegraf input Prometheus plugin to write these metrics to InfluxDB and create a Kafka monitoring dashboard in Chronograf.

For monitoring InfluxDB itself, we collect system and InfluxDB metrics using Telegraf as well.

We plan on ingesting the EFD logs into the logging infrastructure at the Summit and IDF too.


Appendix A - Configuring the Kafka Connect Replicator source connector
======================================================================

We've added the `Kafka Connect Replicator source connector`_ version 5.3.1 to our `Kafka Connect container image <https://github.com/lsst-sqre/kafka-efd-demo/blob/master/k8s-cluster/cp-kafka-connect/Dockerfile>`_ and tested topic replication and schema migration.

In this setup, the `topic replication <https://docs.confluent.io/current/multi-dc-replicator/index.html#multi-dc>`_ works in one direction. The Replicator source connector consumes topics from the source cluster and the Kafka Connect workers produce topics to the destination cluster. Replicated topics are namespaced to indicate their origin. For example, ``summit.{topic}`` indicates that the topic is replicated from the Summit EFD, etc.

Schema migration follows the `continuous migration <https://docs.confluent.io/current/schema-registry/installation/migrate.html#schemaregistry-migrate>`_ model. The Replicator continuously copy schemas from the source cluster to the destination cluster Schema Registry, which is set to IMPORT mode. `Schema translation <https://docs.confluent.io/current/tutorials/examples/replicator-schema-translation/docs/index.html>`_ ensures that subjects are renamed following the topic rename strategy when migrated to the destination Schema Registry.

An example of configuration for the Replicator that includes topic and schema replication with schema translation can be found `here <https://github.com/lsst-sqre/kafka-efd-demo/blob/master/k8s-cluster/cp-kafka-connect/make_replicator_config.sh>`_.


.. figure:: /_static/kafka-replication.svg
   :name: Set up for testing the replicator connector.
   :target: _static/kafka-replication.svg

Topic and schema replication with the Replicator connector.

Note that Kafka Connect ``bootstrap.servers`` configuration must include the URL of the destination Kafka cluster and that the destination Schema Registry must be in IMPORT mode. To initialize the destination Schema Registry to IMPORT mode, first set ``mode.mutability=True`` in the configuration and make sure the destination Schema Registry is empty. See `schema migration configuration <https://docs.confluent.io/current/schema-registry/installation/migrate.html#id1>`_ with the Replicator connector for details.

Confluent's recommendation is to deploy the Replicator source connector at the destination cluster (remote consuming). However, in our current set up some EFD deployments are not visible from the destination cluster due to VPNs and other networking considerations.  Thus, we have deployed the Replicator source connector at the source clusters (remote producing). We have tested the this set up to replicate data from the Summit EFD and Tucson TestStand EFD to our EFD instance running on Google Cloud.  Another good practice is to have a separate Kafka Connect deployment for the Replicator source connector, to isolate this connector from other connectors running in the cluster.


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
