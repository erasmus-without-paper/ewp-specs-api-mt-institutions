Mobility Tool+ Institutions API
===============================

* [What is the status of this document?][statuses]
* [See the index of all other EWP Specifications][develhub]


Summary
-------

This document describes the **Mobility Tool+ Institutions API**.
It is assumed that this API is implemented by only one host managed by
the Directoriate-General Education and Culture Unit (DG EAC) of the European Commission.
However, the specification itself does not limit the number of hosts.

Once implemented by the host, it allows external clients to retrieve general
information on institutions known by this host.


Request method
--------------

 * Requests MUST be made with either HTTP GET or HTTP POST method. Servers MUST
   support both these methods. Servers SHOULD reject all other request methods.

 * Clients are advised to use POST when passing large number of parameters
   (servers MAY set a limit on their GET query string length).


Request parameters
------------------

Parameters MUST be provided in the regular `application/x-www-form-urlencoded`
format.


### `pic` or `erasmus` (repeatable, required)

A list of institution PICs or Erasmus codes the client wants to retrieve information on
(no more than `<max-ids>` items). The requester MUST provide either a list of `pic` values,
or a list of `erasmus` values, **but not both**.

This parameter is *repeatable*, so the request MAY contain multiple occurrences
of it. The server is REQUIRED to process all of them.

Server implementers provide their own chosen value of `<max-ids>` via their manifest entry
(see [manifest-entry.xsd](manifest-entry.xsd)). Clients SHOULD parse this value
(or assume it is equal to `1`).

Clients may retrieve proper PICs or Erasmus codes from other EWP APIs (most often,
the [Registry Service][registry-spec]).

### `eche_at_date` (required)

A date in the `YYYY-mm-dd` format. The Erasmus Charter for Higher Education (ECHE) accreditation
included in the response should be the one valid at this date.


Security
--------

This version of this API uses [standard EWP Authentication and Security,
Version 2][sec-v2]. Server implementers choose which security methods they
support by declaring them in their Manifest API entry.

This API provides data which is also usually accessible to the anonymous public
by other channels. It is RECOMMENDED for server implementers to not be overly
strict on security methods they require (i.e. it is RECOMMENDED to *not*
require extra layers of encryption in requests and responses - TLS seems more
than enough). Server implementers MAY also consider allowing this API to be
accessed by [anonymous clients][cliauth-none].


Handling of invalid parameters
------------------------------

 * General [error handling rules][error-handling] apply.

 * Invalid (unknown) `pic` and `erasmus` values MUST be **ignored**.
   Servers MUST return a valid (HTTP 200) XML response in such cases, but the
   response will simply not contain the information on the unknown entities.
   If all values are unknown, servers MUST respond with an empty `<response>`
   element. This requirement is true even when `<max-ids>` is equal to `1`.

 * If the length of `pic` or `erasmus` list is greater than `<max-ids>`,
   then the server MUST respond with HTTP 400.
   Clients SHOULD split such large requests into a couple of smaller ones.


Response
--------

Servers MUST respond with a valid XML document described by the
[response.xsd](response.xsd) schema. See the schema annotations for further
information.


[develhub]: http://developers.erasmuswithoutpaper.eu/
[statuses]: https://github.com/erasmus-without-paper/ewp-specs-management#statuses
[registry-spec]: https://github.com/erasmus-without-paper/ewp-specs-api-registry
[error-handling]: https://github.com/erasmus-without-paper/ewp-specs-architecture#error-handling
[cliauth-none]: https://github.com/erasmus-without-paper/ewp-specs-sec-cliauth-none
