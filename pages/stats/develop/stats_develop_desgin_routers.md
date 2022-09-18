---
title: Application Design - REST APIs and Routers
sidebar: stats_sidebar
toc: true
permalink: stats_develop_design_routers.html
folder: stats/develop
---

## Introduction

This page describes the implementation of REST APIs in the server application.

## REST Protocols Between Client and Server

If you have a tool that understands documentation in the
[OpenAPI 3.0 Format](https://swagger.io/specification/), you can point it
at a running instance of the server, at `http://localhost:{PORT}/openapi.json`
to see a graphical representation of these APIs.

## Overall Configuration

The server side of these protocols utilize the [Express](https://expressjs.com)
framework.  The overall definition of what protocols are supported are
defined in `src/routers/ExpressApplication.ts`, with the following
key characteristics:
* Definition of an access log that stores information about each HTTP
  request that is processed, in a format compatible with web log data
  from common HTTP servers such as Apache.  The destination for this
  log is defined by the `ACCESS_LOG` environment variable.
* Configuration of standard Express "middleware" that knows how to handle
  JSON data structures.
* Configuration of static routing, used to serve the client application
  to the user's browser.  Any URL that is not recognized by the routers
  defined below are presumed to be static files needed by the browser.
* Top level routes for three paths:
    * */openapi.json* - Handles requests for the OpenAPI documentation.
    * */api* - Top level router for application API requests (see below for more info).
    * */oauth* - Top level router for OAuth login and logout requests.
* Configuration of custom Express "middleware" to handle errors thrown
  by server side service components.

## Per-Model Sub-Routers

In turn, `src/routers/ApiRouter.ts` configures sub-routers for each type
of information that is available via the APIs.  For example, the API calls
for Section model objects are defined in `src/routers/SectionRouter`.  These
Sub-Routers follow the same pattern for standard CRUD operations, and may
also provide some additional endpoints specific to each model type.

Because these are nested inside the API Sub-Router, the actual URL used
by the client is a concatenation of elements from the parent routers.  Thus,
a request for a particular Section (with ID 123) related to facility 3 would be
an HTTP GET to `http://localhost:{PORT}/api/sections/3/123`.

The standard pattern for CRUD operations includes the following endpoints,
where we are talking about a model that is subordinate to a particular Facility
(like Sections):
* `GET /api/sections/:facilityId` - Retrieve all sections that match
  any optional criteria defined in the query parameter.
* `POST /api/sections/:facilityId` - Create a new Section for this Facility.
  The request body will be a JSON representation of the new Section.
  The response body will be a JSON representation of the created Section.
* `DELETE /api/sections/:facilityId/:sectionId` - Delete an existing Section.
* `GET /api/sections/:facilityId/:sectionId` - Find and return an existing Section.
* `PUT /api/sections/:facilityId/:sectionId` - Update an existing Section.
  The request body will be a JSON representation of the updated Section
  (only need to include the fields that are actually changed).
  The response body will be a JSON representation of the resulting update.

In each case, the actual processing to interact with the database is delegated
to an *XxxxxServices* module, which will be discussed below.  The routers are
here to handle all the aspects of the HTTP interactions with the client, while
the services modules handle all the aspects of interactions with the database.

## Permission Checking Middleware

As you peruse the various router definitions, you will notice that many of them
include a "requireXxxxx" middleware, which is executed before the request is
delegated to a Services module.  This middleware enforces requirements that
the request must come from an authorized user (i.e. have an OAuth access token),
which is privileged to perform the operation being requested.  Failure to have
such an access token, or if the access token does not have sufficient privileges
to perform the requested operation, will cause the request to fail with an
HTTP 403 (Forbidden) error.

The most common requirements middleware you will see are:
* *requireRegular* - User must be logged on, and have "regular" permissions
  for the Facility specified in the request.
* *requireAdmin* - User must be logged on, and have "admin" permissions
  for the Facility specified in the request.
* requireSuperuser* - User must be logged on and have "superuser" permissions
  for the entire application.

## HTTP Status Codes and Return Values

The application follows standard HTTP conventions for the HTTP status code
that is returned with the response:
* Success (200 or 201) - The requested operation was completed successfully.
* Bad Request (400) - Server side validation failed.
* Unauthorized (401) - Request must include an access token provided to
  a logged-in User.
* Forbidden (403) - Request came with an access token that does not have
  sufficient permission to perform it.
* Not Found (404) - Request for Model objects with specified IDs found no such data.

Inside the application, error responses are caused by throwing one of the
errors defined in `src/util/HttpErrors.ts`.  The corresponding response body
will include a JSON representation that includes the error message itself
(in the *context* field) and the corresponding HTTP status (in the *status* field).
The thrown error is converted into this type of response using the error handling
middleware mentioned above.
