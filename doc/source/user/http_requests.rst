========================
HTTP(s) requests tracing
========================

OSprofiler does not allow to trace HTTP(s) requests by default.

This OSprofiler version (from `v3.4.4`) allows tracing HTTP(s) requests by adding `X-Trace-Token` into HTTP(s) headers.

How to setup OSprofiler to trace HTTP(s) requests
-------------------------------------------------
You turn on HTTP(s) request tracing by adding `http_request_tracing_token` and `enable_http_request_trace` options into the `profiler` section of the configuration files of the OpenStack services, such as `magnum.conf`, `keystone.conf`, etc.

The two `enable_http_request_trace` and `http_request_tracing_token` options are required to trace HTTP(s) requests, which:

* `enable_http_request_trace`: a boolean value that enables HTTP(s) request tracing for the OpenStack service that you want to do, such as **magnum**, **keystone**, etc.
* `http_request_tracing_token`: a string value that is used to identify the HTTP(s) request tracing token for the OpenStack service that you want to do, such as **magnum**, **keystone**, etc. This token is provided by the Administrators.

The **important** thing that this feature **only works** with the `jaeger` driver.

The below example configuration file is from `magnum.conf`:

.. code-block:: ini

    # other option configurations

    [profiler]
    http_request_tracing_token = super_http_request_tracing_token_xxxxxxxx
    enable_http_request_trace = True
    connection_string = jaeger://localhost:6831
    hmac_keys = SECRET_KEY
    trace_sqlalchemy = True
    enabled = True


Tracing HTTP(s) requests from API
---------------------------------
You can trace HTTP(s) requests from API by adding `X-Trace-Token` into HTTP(s) headers. For example, the below HTTP(s) request is used to get the list of existed clusters from **Magnum** service:

.. code-block:: shell

    curl --location 'http://<HOST>/container-infra/v1/clusters' \
    --header 'X-Auth-Token: <keystone_user_token>' \
    --header 'X-Trace-Token: <http_request_tracing_token_from_adminstrators>'

After the above requests, the OpenStack service which is embeded OSprofiler will attach the `X-Trace-Id` into HTTP(s) headers of responses.

.. image:: https://i.imgur.com/6D7E8BV.png
   :width: 1000

Catch the tracing results from Jaeger dashboard
-----------------------------------------------
Depend on the value from `X-Trace-Id`, you can catch the tracing results from Jaeger dashboard.

For the above result, the `X-Trace-Id` is `03646e85-7183-4f50-9f34-69543df37d8b`, so you can search the tracing results by using the two last grouped digit segments (from `9f34` to end). :

The below images are the result from Jaeger dashboard:

.. image:: https://i.imgur.com/z2Lry9d.png
   :width: 1000

|

The detail of the above tracing result:

.. image:: https://i.imgur.com/VwLG3td.png
   :width: 1000