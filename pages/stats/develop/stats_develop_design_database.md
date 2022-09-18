---
title: Application Design - Database and Models
sidebar: stats_sidebar
toc: true
permalink: stats_develop_design_database.html
folder: stats/develop
---

## Introduction

This page describes the database tables, and the Typescript *Model* classes
that correspond to them.

## Database Tables and Relationships

### Entity-Relationship Diagram

{% include image.html file="stats/stats_database_erd.png" caption="Database Entity Relationship Diagram" %}

In the diagram, the following conventions are used:
* **PK** Primary key.  For the *dailies* table, the primary key is a combination of fields.
* **UK** Unique key.  For the *sections* and *categories* tables, the unique key is a combination of fields.
* **FK** Foreign key.  Represents the primary key of a related table, which must point at an actual row.
* **Bold Column Names** identify fields with NOT NULL constraints.

### Database Tables

The tables in this database are described as follows:

| Table Name | Description                                                                                                                                                                                             |
|------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| users      | One row for each user that is allowed to log in to this application.                                                                                                                                    |
| facilities | One row for each CityTeam Facility (currently five of them).                                                                                                                                            |
| sections   | One row for each group of statistics that will be entered together.  The list of Sections is separate for each Facility.  They are sorted by the *ordinal* value.                                       |
| categories | One row for each individual statistic.  Categories are grouped by Section, and ordered by the *ordinal* value.                                                                                          |
| dailies    | One row for all the statistics gathered for a particular Section, on a particular date.  The *category_ids* and *category_values* are arrays of ID and entered value for each Category in that Section. |

To see the detailed description of each column, the easiest approach is to
look up each table's menu option under the *Admin* menu, then click on one
of the available rows.  The descriptions will be underneath each of the fields.

### Scopes and Permissions

The information visible to (or modifiable by) a particular user
is defined by one or more values in the *scope* column of the *users* table.
To understand what values might be present there, we need to dig a little
deeper.

For a user to have access to any information about a particular CityTeam
*Facility*, they must have an assigned scope that has a prefix of the *scope*
for that Facility, a colon (":"), and a suffix of the *scope* for the
*Section*.  Thus, a user might be assigned scope values like this:
* **pdx:kitchen** - Access to any section with a section scope of *kitchen* for Portland.
* **oak:clothing** - Access to any section with a section scope of *clothing* for Oakland.

In addition to scope combinations for particular sections, the user must
have a general category scope for access to the facility as a whole.  Examples:
* **sfo:regular** - Regular data entry access for sections (as defined by other scopes) for San Francisco.
* **sjc:admin** - Regular access for all sections, plus ability to modify *Section* and *Category* definitions, for San Jose.

Finally, there is a "magic" scope (normally **superuser** but this can be
changed) that allows access to anything and everything for all Facilities,
including the ability to create or update users.

So, a particular user can be assigned as many permission combinations as
needed, separated by spaces in the *scope* column of the *users* table.
He or she should be assigned **{facilityScope}:regular** scope for whatever
CityTeam facility (or facilities) access is granted for, and (unless the user
has **{facilityScope}:admin** permissions), a **{facilityScope}:{sectionScope}**
permission for each *Section* he or she is allowed to enter statistics for.

## Model Classes

Each table has a corresponding Model class in the `src/moels` directory.  These
model classes are all based on the [Sequelize](http://sequelize.org) library,
coupled annotations support from the
[Sequelize Typescript](https://github.com/sequelize/sequelize-typescript#readme)
library.  We use `@Table` decorators to define the characteristics of the
underlying table each Model corresponds to, and `@Column` decorators to
describe each individual column's characteristics.  You will see that these
table and column details match the structure of the underlying database schemas.

In addition, Sequelize supports the definition of several other capabilities:
* Per-model and per-field validation rules, that will throw an error
  if they are violated.
* Foreign key relationships that are enforced by database constraints.
* Definitions of one-to-many and many-to-many relationships between
  tables, coupled with the ability to request that nested related
  data be returned (the underlying SQL SELECT will be a join).

