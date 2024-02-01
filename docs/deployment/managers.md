# Platform managers

## Rules engine

Allows users to execute pre-defined actions based on the data and events in the platform.

These rules are accessible in Settings > Customization > Rules engine

The rules engine is designed to help users automate and streamline their cyber threat intelligence processes.

## History manager

This manager keeps tracks of user/connector interactions on entities in the platform.
It is designed to help users audit and understand the evolution of their CTI data

## Activity manager

The activity manager in OpenCTI is a component that monitors and logs the user actions in the platform such as login, setting update and group update.

## Background task manager

Is a component that handles the execution of tasks, such as importing data, exporting data and mass operations

## Expiration scheduler

The expiration scheduler is responsible for monitoring expired elements in the platform.
It cancels the access rights of expired user accounts and revokes expired indicators from the platform

## Synchronization manager

The synchronization manager enables the data sharing between multiple OpenCTI platforms. It allows the user to create and configure synchronizers which are processes that connect to the live streams of remote OpenCTI platforms and import the data in to the local platform. 

## Retention manager

The retention manager is a component that allows the user to define rules to help delete data in OpenCTI that is no longer relevant or useful. This helps to optimize the performance and storage of the OpenCTI platform and ensures the quality and accuracy of the data.

## Notification manager

The notification manager is a component that allows the user to customize and receive alerts about events/changes in the platform.

!! Is the next paragraph needed here?

It allows the user to create triggers, which are filters that will generate notifications.
It can also create and send digests, which are periodic summaries of the notifications that the user is interested in.
The notifications and digests can be delivered through different channels, such as email, webhooks or the platform interface

More information about the features handled by this component can be found in this [blog post](https://blog.filigran.io/opencti-notifications-and-digests-a-new-powerful-engine-for-the-knowledge-graph-5bb9f04586d9).

## Ingestion manager

The ingestion manager in OpenCTI is a component that managers the ingestion of data from RSS or TAXII feeds

## Playbook manager

Handles the automation scenarios which can be fully customized and enabled by platform administrators to enrich, filter and modify the data created or updated in the platform.

Please read [Playbook automation page](../usage/automation.md) to get more information.

## File index manager

The file indexing manager allows users to search for text content within files uploaded to the platform. 
It extracts and indexes the text content of the files, and stores them in the database.

