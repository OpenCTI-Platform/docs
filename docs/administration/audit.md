## Overview

!!! tip "Enterprise edition"

    Audit unified interface and logs are available under the "Filigran entreprise edition" license.

    Please check https://


OpenCTI audit capability write audit logs that record administrative activities and accesses within your platform. 
Audit logs help you answer "who did what, where, and when?" within your data with the maximum level of transparency. 
Enabling audit logs helps your security, auditing, and compliance entities monitor platform for possible vulnerabilities or external data misuse.

## History vs Audit logs

Audit logs is focusing on user actions that are not directly linked to **knowledge** modification (creation, edition, deletion). Audit logs will produces console/logs files along with user interface elements. 
All knowledge interactions belongs to the **history logs**, that will only produces user interface elements.

Even if this two systems doesn't focus on the same things, OpenCTI provides a unified interface of audit that will let you understand exactly what a user have done in the platform.

TODO Add graphic

### Console / log files
OpenCTI audit capability write audit into console or log files in a JSON format.

```json
TODO add sample log
```

### User interface
OpenCTI is also providing a unified user interface to access audit logs. This dedicated UI provides the easiest experience to consult / analyze / filters all available information.

## Architecture

OpenCTI use different mechanisms to be able to publish actions (audit) or data modification (history)

TODO Add graphic

## Basic audit logs

!!! note "Basic audits"

    With Enterprise edition activated, basic audit logs are always written; you can't configure, exclude, or disable them

### :computer_disk: Data management

#### Ingestion

| <div style="width:200px"></div>  | Create             | Delete                                                 | Edit                              |
|:---------------------------------|:-------------------|:-------------------------------------------------------|:----------------------------------|
| Remote OCTI Streams              | :white_check_mark: | :white_check_mark:                                     | :white_check_mark:                |

#### Data sharing

| <div style="width:200px"></div>  | Create             | Delete                                                 | Edit                              |
|:---------------------------------|:-------------------|:-------------------------------------------------------|:----------------------------------|
| CSV Feeds                        | :white_check_mark: | :white_check_mark:                                     | :white_check_mark:                |
| TAXII Feeds                      | :white_check_mark: | :white_check_mark:                                     | :white_check_mark:                |
| Stream Feeds                     | :white_check_mark: | :white_check_mark:                                     | :white_check_mark:                |

#### Connectors

| <div style="width:200px"></div>  | Create             | Delete                                                 | Edit                              |
|:---------------------------------|:-------------------|:-------------------------------------------------------|:----------------------------------|
| Connectors                       | :cross_mark:       | :cross_mark: Connector<br /> :white_check_mark: Work   | :white_check_mark: State reset    |

#### Background tasks

| <div style="width:200px"></div>  | Create             | Delete                                         | Edit          |
|:---------------------------------|:-------------------|:-----------------------------------------------|:--------------|
| Background tasks                 | :white_check_mark: | :white_check_mark:                             | :prohibited:  |

### :gear: Settings

#### Security

| <div style="width:200px"></div> | Create             | Delete             | Edit               |
|:--------------------------------|:-------------------|:-------------------|:-------------------|
| Roles                           | :cross_mark:       | :cross_mark:       | :cross_mark:       |
| Groups                          | :white_check_mark: | :white_check_mark: | :white_check_mark: |
| Users                           | :white_check_mark: | :white_check_mark: | :white_check_mark: |
| Sessions                        | :cross_mark:       | :cross_mark:       | :cross_mark:       |
| Settings                        | :cross_mark:       | :cross_mark:       | :cross_mark:       |

#### Entity types

| <div style="width:200px"></div> | Create        | Delete        | Edit          |
|:--------------------------------|:--------------|:--------------|:--------------|
| Entity types                    | :prohibited:  | :prohibited:  | :cross_mark:  |

#### Retention policies

| <div style="width:200px"></div>  | Create             | Delete              | Edit                |
|:---------------------------------|:-------------------|:--------------------|:--------------------|
| Retention policies               | :white_check_mark: | :white_check_mark:  | :white_check_mark:  |

#### Rules engine

| <div style="width:200px"></div>  | Create             | Delete             | Edit                |
|:---------------------------------|:-------------------|:-------------------|:--------------------|
| Inferences rules                 | :cross_mark:       | :cross_mark:       | :white_check_mark:  |

#### Labels and attributes

| <div style="width:200px"></div> | Create             | Delete             | Edit                |
|:--------------------------------|:-------------------|:-------------------|:--------------------|
| Status templates (workflow)     | :white_check_mark: | :white_check_mark: | :white_check_mark:  |

### Accesses audits

TODO

### Knowledge

TODO

## Extended audit logs

!!! note "Extended audits"

    With Enterprise edition activated, extented activity audit logs are written only if you activate the feature for a subset of users / groups or organizations

### Knowledge

| <div style="width:200px"></div>  | Upload             | Download           | Delete       |
|:---------------------------------|:-------------------|--------------------|:-------------|
| Files                            | :white_check_mark: | :white_check_mark: | :cross_mark: |


| <div style="width:200px"></div> | Read               | Create                            | Delete                            |
|:--------------------------------|:-------------------|-----------------------------------|:----------------------------------|
| Entity or relation              | :white_check_mark: | :white_check_mark: (from history) | :white_check_mark: (from history) |