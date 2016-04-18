Outgoing Mobility Remote Update API
===================================

* [What is the status of this document?][statuses]
* [See the index of all other EWP Specifications][develhub]


Summary
-------

This document describes the **Outgoing Mobility Remote Update API**. This API is
implemented by the sending institution. It allows the receiving institution to
update the Outgoing Mobility object in certain ways (add new entries to its
history).


Request method
--------------

 * Requests MUST be made with HTTP POST method. Servers SHOULD reject all other
   request methods.


Request parameters
------------------

Parameters MUST be in the `application/x-www-form-urlencoded` format (for POST
requests).


### `mobility_id` (required)

ID of Outgoing Mobility object the client wants to modify.

Note, that `mobility_id` identifiers are unique within the entire EWP Network
(see discussion
[here](https://github.com/erasmus-without-paper/general-issues/issues/10)), so
there is no reason for the `hei_id` parameter to exist.


### `sync_verifier` (required)

Integer. The current length of the Outgoing Mobility object's history, i.e. the
number of child elements of the `<timeline>` element. "Current" means "before
this new change is made".

This parameter is required in order to verify that the requester is aware of
the current state of the mobility (to prevent race conditions, i.e. when a new
history entry is appended by the sending HEI and the receiving HEI at the same
time).


### `entries_to_append` (required)

This parameter describes XML elements to be appended to the Outgoing Mobility's
`<timeline>`. The value of this parameter MUST be a valid XML document
described by the [entries_to_append.xsd](entries_to_append.xsd) schema. See the
schema annotations for further information.


Permissions
-----------

 * If the caller is receiving HEI of the Outgoing Mobility object identified by
   `mobility_id` parameter, then he MUST be allowed to call this API.

 * If the caller is the sending HEI (yourself), then he MAY be allowed
   access to read the mobility data. (It seems reasonable, but it's really an
   internal decision of your team.)

 * All other callers MUST NOT be allowed access to mobility data.


Handling of invalid parameters
------------------------------

 * General [error handling rules][error-handling] apply.

 * Invalid or unknown `mobility_id` values MUST result in a HTTP 400 error
   response.

 * If object identified by `mobility_id` exists, but the requester doesn't have
   the permission to modify it, then server MUST respond with a HTTP 403 error
   response.

 * Invalid `entries_to_append` values (e.g. when the value does not conform to
   the proper XML Schema) MUST result in a HTTP 400 error response.

 * Invalid (out-of-date) values of `sync_verifier` MUST result in HTTP 409
   error response. Clients which receive this error should refresh their stale
   Outgoing Mobility objects and resolve all conflicts before resubmitting
   their requests.


Response
--------

Servers MUST respond with a valid XML document described by the [response.xsd]
(response.xsd) schema. See the schema annotations for further information.


[develhub]: http://developers.erasmuswithoutpaper.eu/
[statuses]: https://github.com/erasmus-without-paper/ewp-specs-management#statuses
[registry-spec]: https://github.com/erasmus-without-paper/ewp-specs-api-registry
[discovery-api]: https://github.com/erasmus-without-paper/ewp-specs-api-discovery
[echo]: https://github.com/erasmus-without-paper/ewp-specs-api-echo
[error-handling]: https://github.com/erasmus-without-paper/ewp-specs-architecture#error-handling
[institutions-api]: https://github.com/erasmus-without-paper/ewp-specs-api-institutions
