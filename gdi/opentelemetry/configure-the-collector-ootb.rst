.. _otel-configuration-ootb:

****************************************************************
Collector default configuration
****************************************************************

.. meta::
      :description: Configure the Splunk Distribution of OpenTelemetry Collector. There are a variety of default configuration files available, as well additional components that can be configured.

The Collector configuration is stored in a :new-page:`YAML file <https://yaml.org/>` and specifies the behavior of the different components and services.

.. include:: /_includes/collector-components.rst

See an overview of the elements and pipelines in the default configuration in the following sections.

Default configuration 
========================================================

This is the default configuration file for the Linux (Debian/RPM) and Windows Installer collector packages:

.. github:: yaml
  :url: https://raw.githubusercontent.com/signalfx/splunk-otel-collector/main/cmd/otelcol/config/collector/agent_config.yaml

Default pipelines
========================================================

By default, ingested data follows these pipelines.

Default pipelines for logs 
----------------------------------------------------------------------------

The following diagram shows the default logs pipeline:

.. mermaid:: 

   flowchart LR
      %% LR indicates the direction (left-to-right)

      %% You can define classes to style nodes and other elements
      classDef receiver fill:#00FF00
      classDef processor fill:#FF9900
      classDef exporter fill:#FF33FF

      %% Each subgraph determines what's in each category
      subgraph Receivers
         direction LR
         logs/signalfx/signalfx/in:::receiver
         logs/signalfx/smartagent/processlist:::receiver
         logs/fluentforward:::receiver
         logs/otlp:::receiver
      end

      subgraph Processor
         direction LR
         logs/signalfx/memory_limiter:::processor --> logs/signalfx/batch:::processor --> logs/signalfx/resourcedetection:::processor
         logs/memory_limiter:::processor --> logs/batch:::processor --> logs/resourcedetection:::processor
      end

      subgraph Exporters
         direction LR
         logs/signalfx/signalfx/out:::exporter
         logs/splunk_hec:::exporter
      end

      %% Connections beyond categories are added later
      logs/signalfx/signalfx/in --> logs/signalfx/memory_limiter
      logs/signalfx/resourcedetection --> logs/signalfx/signalfx/out
      logs/signalfx/smartagent/processlist --> logs/signalfx/memory_limiter
      logs/fluentforward --> logs/memory_limiter
      logs/resourcedetection --> logs/splunk_hec
      logs/otlp --> logs/memory_limiter

Learn more about these receivers:

* :ref:`signalfx-receiver`
* :ref:`processlist`
* :ref:`fluentd-receiver`

Learn more about these processors:

* :ref:`batch-processor`
* :ref:`resourcedetection-processor`

Learn more about these exporters:

* :ref:`signalfx-exporter`
* :ref:`splunk-hec-exporter`


Default pipelines for metrics 
----------------------------------------------------------------------------

The following diagram shows the default metrics pipeline:

.. mermaid:: 

   flowchart LR
      %% LR indicates the direction (left-to-right)

      %% You can define classes to style nodes and other elements
      classDef receiver fill:#00FF00
      classDef processor fill:#FF9900
      classDef exporter fill:#FF33FF

      %% Each subgraph determines what's in each category
      subgraph Receivers
         direction LR
         metrics/hostmetrics:::receiver
         metrics/otlp:::receiver
         metrics/signalfx/in:::receiver
         metrics/smartagent/signalfx-forwarder:::receiver
         metrics/internal/prometheus/internal:::receiver
      end

      subgraph Processor
         direction LR
         metrics/memory_limiter:::processor --> metrics/batch:::processor --> metrics/resourcedetection:::processor
         metrics/internal/memory_limiter:::processor --> metrics/internal/batch:::processor --> metrics/internal/resourcedetection:::processor
      end

      subgraph Exporters
         direction LR
         metrics/signalfx/out:::exporter
         metrics/internal/signalfx/out:::exporter
      end

      %% Connections beyond categories are added later
      metrics/hostmetrics --> metrics/memory_limiter
      metrics/resourcedetection --> metrics/signalfx/out
      metrics/otlp --> metrics/memory_limiter
      metrics/signalfx/in --> metrics/memory_limiter
      metrics/smartagent/signalfx-forwarder --> metrics/memory_limiter
      metrics/internal/prometheus/internal --> metrics/internal/memory_limiter
      metrics/internal/resourcedetection --> metrics/internal/signalfx/out



Learn more about these receivers:

* :ref:`host-metrics-receiver`
* :ref:`signalfx-receiver`
* :ref:`signalfx-forwarder`
* :ref:`prometheus-receiver`

Learn more about these processors:

* :ref:`batch-processor`
* :ref:`resourcedetection-processor`

Learn more about these exporters:

* :ref:`signalfx-exporter`

Default pipelines for traces 
----------------------------------------------------------------------------

The following diagram shows the default traces pipeline:

.. mermaid:: 

   flowchart LR
      %% LR indicates the direction (left-to-right)

      %% You can define classes to style nodes and other elements
      classDef receiver fill:#00FF00
      classDef processor fill:#FF9900
      classDef exporter fill:#FF33FF

      %% Each subgraph determines what's in each category
      subgraph Receivers
         direction LR
         traces/jaeger:::receiver
         traces/otlp:::receiver
         traces/smartagent/signalfx-forwarder:::receiver
         traces/zipkin:::receiver
      end

      subgraph Processor
         direction LR
         traces/memory_limiter:::processor --> traces/batch:::processor --> traces/resourcedetection:::processor
      end

      subgraph Exporters
         direction LR
         traces/sapm:::exporter
         traces/signalfx/out:::exporter
      end

      %% Connections beyond categories are added later
      traces/jaeger --> traces/memory_limiter
      traces/otlp --> traces/memory_limiter
      traces/smartagent/signalfx-forwarder --> traces/memory_limiter
      traces/zipkin --> traces/memory_limiter
      traces/resourcedetection --> traces/sapm
      traces/resourcedetection --> traces/signalfx/out


Learn more about these receivers:

* :ref:`jaeger-grpc`
* :ref:`signalfx-forwarder`

Learn more about these processors:

* :ref:`batch-processor`
* :ref:`resourcedetection-processor`

Learn more about these exporters:

* :ref:`splunk-apm-exporter`
* :ref:`signalfx-exporter`

Learn more
========================================================

See also the following documents:

* :ref:`ootb-metrics-k8s`
* :ref:`otel-collector-scenario`
* :ref:`otel-install-platform` 
* :ref:`Troubleshooting <otel-troubleshooting>`
