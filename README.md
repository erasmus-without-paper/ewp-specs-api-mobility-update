OBSOLETE - Outgoing Mobility Remote Update API
==============================================

* [What is the status of this document?][statuses]
* [See the index of all other EWP Specifications][develhub]


Summary
-------

**OBSOLETE: This proposal has been merged with [Outgoing Mobilities API]
[mobilities-api].**

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

Parameters MUST be provided in the regular `application/x-www-form-urlencoded`
format.


### `sending_hei_id` (required)

An identifier of the institution which is the master of the Outgoing Mobility
object the client wants to modify. This parameter MUST be required by the
server even if it covers only a single institution.

Note, that `mobility_id` identifiers should be unique within the entire EWP
Network, not only within a single sending HEI (see discussion
[here](https://github.com/erasmus-without-paper/general-issues/issues/10)).
That might imply that, in theory, the `sending_hei_id` parameter could be
skipped. However, it still exists, and it is required, for performance and/or
clarity reasons (see discussion [here]
(https://github.com/erasmus-without-paper/ewp-specs-api-echo/issues/3#issuecomment-228278115)).


### `mobility_id` (required)

ID of Outgoing Mobility object the client wants to modify. It must "belong" to
the sending HEI provided in the `sending_hei_id` parameter.


### `sync_verifier` (required)

Integer. The current revision of the Outgoing Mobility object's timeline.
"Current" means "before this new change is made".

This parameter is required in order to prevent [edit conflicts]
(https://en.wikipedia.org/wiki/Edit_conflict). Before proceeding with the
update, the server is REQUIRED to verify if the requester is in possession of
the current version of the Outgoing Mobility object.


### `entries_to_append` (required)

This parameter describes XML elements to be appended to the Outgoing Mobility's
`<timeline>`. The value of this parameter MUST be a valid XML document
described by the [entries_to_append.xsd](entries_to_append.xsd) schema. See the
schema annotations for further information.


Permissions
-----------

 * If the caller covers the receiving HEI of the Outgoing Mobility identified
   by `mobility_id` parameter, then he MUST be allowed to call this API.

 * If the caller covers the sending HEI of the mobility, then he MAY be allowed
   to call this API. (It seems reasonable, but we leave this decision to your
   team.)

 * All other callers MUST NOT be allowed access to this API.


Handling of invalid parameters
------------------------------

 * General [error handling rules][error-handling] apply.

 * Invalid or unknown `mobility_id` values MUST result in a HTTP 400 error
   response.

 * If object identified by `mobility_id` exists, but it is not visible to the
   requester (and thus, not modifiable too), then the server also MUST respond
   with HTTP 400 error.

 * Invalid `entries_to_append` values (e.g. when the value does not conform to
   the proper XML Schema) MUST result in a HTTP 400 error response.

 * Invalid (out-of-date) values of `sync_verifier` MUST result in HTTP 409
   error response. Clients which receive this error should refresh their stale
   Outgoing Mobility object and resolve all conflicts before resubmitting
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
[mobilities-api]: https://github.com/erasmus-without-paper/ewp-specs-api-mobilities
