# Project name

I'm using this project as a way of exploring architecting an event processing system.
My hope is to produce something which resolves issues with past projects I've worked on, so perhaps I can do a talk contrasting these experiences.

`¯\_(ツ)_/¯`

## Background

The premise is that there is a company who is wanting to build a suite of IoT products and make them available to consumers as a subscription service.
For example, a motion sensor that is used protect a house by keeping an eye out for intruders and sending alerts using [NB-IoT](https://en.wikipedia.org/wiki/Narrowband_IoT).

The products share common ground in that they all generate alerts based on telemetry.

### Forces

* Highest frequency of requests will be device telemetry.
* Reasonably frequent changes to business processes for device telemetry.
* Business processes for device telemetry might need to operate across many messages (i.e. calculating a rolling average).

## Design

### Domain Concepts

```none
+--------+                  +---------+                   +---------+
|  User  *----Membership----* Account 1----Association----*  Device |
+--------+                  +---------+                   +---------+
```

#### Users

Users represent the carbon-based lifeform.

#### Memberships

Users can have memberships for many accounts.

#### Accounts

Accounts represent a billable entity.

#### Associations

Devices belong to one account.

#### Devices

Devices send telemetry including measurements of the environment (e.g. current temperature) and their own status (e.g. battery level).

### Application Concepts

#### Workflows

Business logic that acts on an event and may lead to a notification.

#### Notifications

Mailboxes for outgoing events (e.g. push notification, emails, webhook). 

### Services

```none
+----------------+        +-----------------+       +-----------------+
| Ingest         ---------> Statuses        --------> Notifications   |
|                |        +-----------------+       |                 |
|                |        +-----------------+       |                 |
|                ---------> Activities      -------->                 |
|                |        +-----------------+       |                 |
|                |        +-----------------+       |                 |
|                ---------> Emergencies     -------->                 |
|                |        +-----------------+       |                 |
|                |        +-----------------+       |                 |
|                ---------> Etcetera        -------->                 |
+-------^--------+        +-----------------+       +--------^--------+
        |                                                    |         
        |                                                    |         
        |        +----------------------------------+        |         
        |        | Accounts                         |        |         
        |        |                                  |        |         
        +---------                                  ---------+         
                 |                                  |                  
                 |                                  |                  
                 +----------------------------------+                  
```

**Dependency diagram** showing the services and their dependencies.