.. _aws-ts-metric-streams:

******************************************************
Troubleshoot Splunk-managed Metric Streams
******************************************************

.. meta::
  :description: Troubleshoot AWS Metric Streams related issues.

See the following topics when experiencing AWS Metric Streams related issues.

.. note:: See also :ref:`aws-troubleshooting` and :ref:`aws-ts-ms-aws`.

I've enabled Splunk-managed Metric Streams in my AWS integration but I do not see any metrics streaming in
==========================================================================================================

After all the required IAM permissions are in place, your AWS integration is configured, and one of the :ref:`CloudFormation templates <aws-cloudformation>` has been deployed, Splunk Observability Cloud starts to create CloudWatch Metric Streams objects in your AWS account. It might take up to 15 minutes for metrics to start streaming.

If you're experiencing issues streaming metrics, check the following: 

* In the AWS CloudWatch Metric Streams console, verify that Metric Streams (automatically created by Splunk Observability Cloud) match the ``splunk-metric-stream-<integration-id>`` pattern. If Metric Streams are not present, review the Metric Streams set-up procedure.

* CloudFormation parameter values:
  
  * Set ``SplunkIngestUrl`` to the value shown in the :guilabel:`Real-time Data Ingest Endpoint` section under :strong:`Organizations` in your profile. For example, https://ingest.signalfx.com.
  
    Note: Don't include the :strong:`/v1/cloudwatch_metric_stream` endpoint path in ``SplunkIngestUrl``. However, when checking metric stream destination details you should see full URL with the endpoint included, e.g. `https://ingest.signalfx.com/v1/cloudwatch_metric_stream`.

  * ``SplunkAccessToken`` needs to be a valid organization access token with ``INGEST`` authorization scope. You can find access tokens in the :strong:`Access Tokens` page in the Splunk Observability Cloud settings.

* Make sure the tag ``splunk-metric-streams-firehose = <your_AWS_region>`` is present in your Kinesis Data Stream.

* In the AWS Kinesis Streams console, validate that metrics are being delivered to the Kinesis Stream created by the CloudFormation template.

* In the AWS S3 bucket used by Kinesis Firehose, check if there are any records that could not be delivered to Splunk Observability Cloud.

How can I track invalid Metric Streams?
====================================================================================================

Splunk Observability Cloud provides out-of-the-box dashboard to help you track invalid or faulty Metric Streams, including:

* Streams without a matching AWS integration on the Observability side.
* Streams from a disabled integration or Metric Stream feature.

To access these dashboards, go to :guilabel:`Dashboards > Organization metrics > Cloud integrations`, and look for :guilabel:`Invalid AWS CloudWatch Metric Streams`. 

To learn more about built-in content, see :ref:`built-in-dashboards`.

How does Splunk-managed Metric Streams clean-up work? How can I try to clean up Metric Streams again?
=====================================================================================================

When you deactivate Splunk-managed Metric Streams (or an entire AWS integration), Splunk Observability Cloud attempts to remove all CloudWatch Metric Streams that had been created. This process might take up to 15 minutes.

The clean-up procedure might fail if you remove IAM permissions or due to throttling, for example if there are too many API calls to delete Metric Streams objects.

To retry the clean-up process, you have two options:

* Splunk Observability Cloud UI (beta feature - limited availability): Go to the context menu in the integration list and select Cleanup. 
* API: Set ``metricStreamsSyncState`` to the ``CANCELLING`` state.

Assisted Splunk-managed Metric Streams clean-up failed. How do I clean up Metric Streams manually?
====================================================================================================

To manually remove Metric Streams:

#. Go to the AWS CloudWatch Metric Streams console in each region where you deployed a CloudFormation template. 
#. Remove all Metric Streams with a name starting with the ``splunk-metric-stream`` prefix followed by a numeric id.

.. image:: /_images/gdi/aws-ts-ms-remove.png
   :width: 100%
   :alt: This image shows how to manually remove Metric Streams.

Optionally, go to the CloudFormation console and remove the entire Splunk Observability Cloud stack.


