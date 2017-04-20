.. _rate_limits:

===========
Rate Limits
===========

Rate limiting ensures that no single SmartApp or Device Handler will consume too many shared resources.

Rate limits apply to **all** SmartApps and Device Handlers.

-----

SmartApp and Device Handler rate limits
---------------------------------------

SmartApps and Device Handlers are monitored for excessive resource utilization on two measures: *Execution count limits* and *Execution time limits*.

Execution count limits
^^^^^^^^^^^^^^^^^^^^^^

SmartApps and Device Handlers are subject to the following execution count limits.
These limits are per *installed SmartApp or Device Handler*.

===================== =========== ===========
Execution count limit Time window Description
===================== =========== ===========
250 executions        60 seconds  A maximum of 250 executions per minute is allowed for each installed SmartApp or Device Handler.
===================== =========== ===========

If the limit is exceeded, an error will be displayed in Live Logging, and no further executions for this installed SmartApp or Device Handler will occur until the current 60-second time window expires.

Execution time limits
^^^^^^^^^^^^^^^^^^^^^

These execution time limits apply to SmartApps and Device Handlers:

=============================== =====
What                            Limit
=============================== =====
Method execution time           20 seconds
Total continuous execution time 40 seconds
=============================== =====

If these limits are exceeded, the current execution will be suspended.

----

.. _web_services_rate_limiting:

Web services rate limit headers
-------------------------------

SmartApps and Device Handlers that expose RESTful APIs are subject to the same rate limits as documented above.
The SmartThings platform will set three HTTP headers on the response for every inbound API call, so that a client may understand the current rate limit status.

======================= ===========
Header                  Description
======================= ===========
``X-RateLimit-Limit``   The total enforced rate limit (250)
``X-RateLimit-Current`` The number of executions within the current rate limit time window, for this installed SmartApp or Device Handler.
``X-RateLimit-TTL``     The time remaining (in seconds) before the current rate limit window resets, for this installed SmartApp or Device Handler.
======================= ===========

If the rate limit is exceeded, the following response is sent to the client:

=========================== ===============================================================================================================
HTTP Response Code          Error Response
=========================== ===============================================================================================================
``429 (Too Many Requests)`` ``{"error": true, "type": "RateLimit", "message": "Please try again later"}``
=========================== ===============================================================================================================

----

.. _sms_rate_limits:

SMS rate limits
---------------

The following limits apply to sending SMS messages:

========================================== ===========
Limit                                      If exceeded
========================================== ===========
15 SMS messages per number, per 60 seconds No additional SMS messages will be sent until the next minute.
========================================== ===========

.. note::

    This limit applies **per number**, not per SmartApp or user.

----

.. _parent_child_count_limit:

Parent-child relationship limit
-------------------------------

The number of child SmartApps or child devices that a SmartApp or Device Handler may have are subject to the following limits:

=================== ===========
Maximum child count Description
=================== ===========
500                 A SmartApp may have at most a combination of 500 child SmartApps or Devices. A Device Handler may have at most 500 child Devices.
=================== ===========

If this limit is exceeded, an exception is thrown and will be displayed in Live Logging.
If initiated from within the mobile app, an error will be seen in the mobile application as well.

----

Avoiding rate limits
--------------------

While SmartThings rate limits are quite high compared to other service platforms, the event-driven nature of SmartThings can result in SmartApps or Device Handlers that may (unintentionally) reach this limit.
It is important to reason carefully about your code, think of worst-case scenarios, and monitor Live Logging when testing to reduce the liklihood of being rate limited.

Here are some common pitfalls to watch out for:

- A SmartApp may subscribe to a large number of "chatty" devices, causing the execution limit to be reached. For example, DLNA devices may be particularly chatty, and frequently changing energy/power values may cause the rate limit to be exceeded.
- Service Manager SmartApps that may be called by their child devices may reach the execution limit, if there are a number of child devices and/or they call the parent in response to frequent events.
- Synchronous (blocking) HTTP requests may hit the execution time rate limit, depending on the third party response time. Avoid this possibility by using :ref:`async_http_guide`.
- It's possible to create an infinite loop of events. For example, subscribing to both "on" and "off" events, and the "on" command triggers the "off" event and vice versa - leading to a never-ending chain of event handlers being called.
- Pay attention to any looping logic around creating child devices or SmartApps. Any error in the looping logic might result in creating too many children, which could encounter the parent-child limit.
