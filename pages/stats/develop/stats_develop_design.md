---
title: Application Design Overview
sidebar: stats_sidebar
toc: false
permalink: stats_develop_design.html
folder: stats/develop
---

## Introduction

The application can be described as having four basic components:

TODO - pretty picture (with a brief thing in between each component
describing the technology used).

* **User** - Operates the interactive portion of the application using any modern web browser that supports HTML5 (including web browser capabilities on tablets and phones).
* **Client** - The portion of the application that runs inside the user's web browser. rendering HTML and executing Javascript.
* **Server** - The portion of the application that runs on a backend server, receiving network requests from the client, performing corresonding data retrieval or storage in the database, and returning results to the client.  This portion can operate on a laptop (indeed, you will be running it locally when doing development), on a server connected to a local area network, or on a cloud platform.
* **Database** - The place that data from the application is persisted, even if the application is shut down.  It executes SQL commands to perform its tasks.

This (and subsequent) pages document the design of the various application
components.  For the server side, the following pages are available:
* [Database Tables and Models](stats_develop_design_database) - Database tables
  and the corresponding Model implementations in the server application.
* [REST APIs and Routers](stats_develop_design_routers) - Router implementations that
  define the API endpoints for the server application.
* [Services](stats_develop_design_services) - Service implementations that
  interact with the database via the defined Models.

## Background

To be most effective, developers should become familiar with the
following core technologies (the referenced links point at some canonical
information sources, but there are thousands of other resources available
on the web as well):
* [Javascript](https://javascript.info) - Programming language used on both client and server.
* [Typescript](https://www.typescriptlang.org) - Add-on capabilities that bring type safety and other features to Javascript based applications.
* [Hypertext Markup Language (HTML)](https://developer.mozilla.org/en-US/docs/Web/HTML) - HTML is the language used to create user interfaces on the client, as well as interact with Javascript/Typescript logic to enable interactivity.
* [Node.JS](https://nodejs.org) - Fundamental architecture for server side Javascript/Typescript applications.
* [React](https://reactjs.org) - Fundamental architecture for client side Javascript/Typescript applications.
* [React Bootstrap](https://react-bootstrap.github.io) - Library of React components that use the Bootstrap CSS capabilities.
* [Hypertext Transfer Protocol (HTTP)](https://developer.mozilla.org/en-US/docs/Web/HTTP/Overview) - Protocol defining how client and server environments communicate with each other.
* [Representational State Transfer (REST)](https://restfulapi.net) - Architectural style for organizing HTTP message formats (and URI destinations).  This application conforms to REST design principles.
* [Structured Query Language (SQL)](https://postgresql.org/docs/14/index.html) - SQL is the most popular standard for interacting with a database.  This application uses Postgres as its database implementation.
* [Javascript Object Notation (JSON)](https://www.json.org) - The message format used on network messages between the client and server portions of the application.  Also, very similar to the way complex objects are represented in Javascript/Typescript.
* [GitHub](https://github.com) - GitHub is the storage host for this project, as well as many thousands of others.  It features the Git repository management application, with lots of extra goodies.

