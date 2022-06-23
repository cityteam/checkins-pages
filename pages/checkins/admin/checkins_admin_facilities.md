---
title: Manage Facilities
sidebar: checkins_sidebar
toc: true
permalink: checkins_admin_facilities.html
folder: checkins/admin
---

## Introduction

A *Facility* is a physical CityTeam location, which is equipped to accomodate
overnight Guests.  This application will be used to check in Guests each evening,
keeping track of which sleeping mats (or other accomodations) each Guest has been
assigned to.

## List View

When you select the `Admin` -> `Facilities` view, you will see a list of previously
defined Facilities.  If you are the **superuser** user, you will see all of them.
Otherwise, you will only see the Facility (or Facilities) for which your username
has permissions to access.

{% include image.html file="checkins/facilities-first.png" caption="Facilities List" %}

The database has been preloaded with Facility information about all current
CityTeam Facilities, so you should normally not need to make any changes.

## Details View

If you are the **superuser** user, you can add a new Facility by clicking the
`Add` button, or edit an existing Facility by clicking on it in the list.  Otherwise,
these controls will be disabled.

{% include image.html file="checkins/facilities-second.png" caption="Facility Details" %}

## Field Definitions

| Field Name    | Required | Description |
|---------------|:--------:| ----------- |
| Name          |   Yes    | Formal name of this Facility.  Must be globally unique. |
| Scope         |   Yes    | Prefix for permission scope.  See below for more info.  Must be globally unique. |
| Address 1     |   No     | First line of street address. |
| Address 2     |   No     | Second line of street address. |
| City          |   No     | City name of street address. |
| State         |   No     | State abbreviation of street address.  If present, must be valid. |
| Zip Code      |   No     | Zip code of street address. |
| Email Address |   No     | Contact email address of this Facility. |
| Phone Number  |   No     | Phone number of this Facility (including area code). |
|  Active       |   Yes    | Flag indicating whether this Facility is active or not. |

The *Scope* field deserves some particular attention.  When a User is registered,
they will be assigned one or more *Scope* values of the form

<p align="center">{FACILITY_SCOPE}:{PERMISSION}</p>

where {FACILITY_SCOPE} is the value listed on the Facility, and {PERMISSION} is either
*admin* or *regular*.  It is possible, but not common, to assign more than one such
scope to a particular user.

Return to the [Overview](checkins_admin_overview) or follow one of the links at the side
of the page to explore other administrative information that you might encounter.
