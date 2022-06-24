---
title: Manage Templates
sidebar: checkins_sidebar
toc: true
permalink: checkins_admin_templates.html
folder: checkins/admin
---

## Introduction

A *Template* is a description of mats (or other sleeping arrangements) that can
be assigned to overnight Guests.  Each day, prior to beginning the checkin
process, you will need to select one of the defined Templates for this Facility,
and generate the available mats (as described by the *All Mats* field) to be
used that day.

A user with *admin* permissions on a Facility can edit Templates, while
a user with *regular* permissions on a Facility can see existing Template
definitions but not modify them.

## List View

When you select the `Admin` -> `Templates` view, you will see a list of previously
defined Templates for this Facility.

{% include image.html file="checkins/templates-first.png" caption="Templates List" %}

## Details View

When an administrator selects a particular Template by clicking on it, they
will see the corresponding details for that Template.

{% include image.html file="checkins/templates-second.png" caption="Template Details" %}

## Field Definitions

| Field Name     | Required | Description                                      |
| -------------- | :------: |--------------------------------------------------|
| Name           | Yes      | Name of this Template.  Must be unique.          |
| Comments       | No       | General comments about this Template.            |
| All Mats       | Yes      | List of mat numbers to be generated.  See below. |
| Handicap Mats  | No       | List of mats that are handicap accessible.       |
| Socket Mats    | No       | List of mats that are close to an electrical socket. |
| Work Mats      | No       | List of mats generally reserved for special uses. |

The *All Mats* field is a list of mat number ranges (separated by '**-**'), or
individual mat numbers (separated by '**,**').  You may define as many ranges
or individual mats as you need, but the following rules apply:
* You cannot specify a single mat number more than once.
* Mat numbers must be listed in ascending order.

The other mat number fields are optional, but (when used) specify a letter that
will be added after the mat number itself in the checkin process views.  For
example, a mat that is listed as a handicap mat and a socket mat might appear
as **3HS** instead of just **3** in the list of available mats.
