# Users and Role Based Access Control

## Introduction

In OpenCTI, the RBAC system not only related to what users can do or cannot do in the platform (aka. `Capabilities`) but also to the system of [data segregation](segregation.md). Also, platform behavior such as default home dashboards, default triggers and digests as well as default hidden menus or entities can be defined across groups and organizations.

## High level design

![RBAC](assets/rbac.png)

## Roles 

Roles are used in the platform to grant the given groups with some **capabilities** to define what users in those groups can do or cannot do.

### List of capabilities

| Capability                                                         | Description                                                                             |
|:-------------------------------------------------------------------|:----------------------------------------------------------------------------------------|
| `Bypass all capabilities`                                          | Just bypass everything including data segregation and enforcements.                     |
| `Access knowledge`                                                 | Access in read-only to all the knowledge in the platform.                               |
| &nbsp;&nbsp;`Access to collaborative creation`                     | Create notes and opinions (and modify its own) on entities and relations.               |
| &nbsp;&nbsp;`Create / Update knowledge`                            | Create and update existing entities and relationships.                                  |
| &nbsp;&nbsp;&nbsp;&nbsp;`Restrict organization access`             | Share entities and relationships with other organizations.                              |
| &nbsp;&nbsp;&nbsp;&nbsp;`Delete knowledge`                         | Delete entities and relationships.                                                      |
| &nbsp;&nbsp;`Upload knowledge files`                               | Upload files in the `Data` and `Content` section of entities.                           |
| &nbsp;&nbsp;`Download knowledge export`                            | Download the exports generated in the entities (in the `Data` section).                 |
| &nbsp;&nbsp;`Ask for knowledge enrichment`                         | Trigger an enrichment for a given entity.                                               |
| `Access exploration`                                               | Access to workspaces whether custom dashboards or investigations.                       |
| &nbsp;&nbsp;`Create / Update exploration`                          | Create and update existing workspaces whether custom dashboards or investigations.      |
| &nbsp;&nbsp;&nbsp;&nbsp;`Delete exploration`                       | Delete workspaces whether custom dashboards or investigations.                          |
| `Access connectors`                                                | Read information in the `Data > Connectors` section.                                    |
| &nbsp;&nbsp;`Manage connector state`                               | Reset the connector state to restart ingestion from the beginning.                      |
| `Access data sharing & ingestion`                                  | Access and consume data such as TAXII collections.                                      |
| &nbsp;&nbsp;`Manage data sharing & ingestion`                      | Create, update and delete data such as TAXII collections.                               |
| &nbsp;&nbsp;`Manage CSV mappers`                                   | Create, update and delete CSV mappers.                                                  |
| `Access administration`                                            | Access and manage overall parameters of the platform in `Settings > Parameters`.        |
| &nbsp;&nbsp;`Manage credentials`                                   | Access and manage roles, groups, users, organizations and security policies.            |
| &nbsp;&nbsp;`Manage marking definitions`                           | Update and delete marking definitions.                                                  |
| &nbsp;&nbsp;`Manage labels & Attributes`                           | Update and delete labels, custom taxonomies, workflow and case templates.               |
| `Connectors API usage: register, ping, export push ...`            | Connectors specific permissions for register, ping, push export files, etc.             |
| `Connect and consume the platform streams (/stream, /stream/live)` | List and consume the OpenCTI live streams.                                              |
| `Bypass mandatory references if any`                               | If external references enforced in a type of entity, be able to bypass the enforcement. |


### Manage roles

You can manage the roles in `Settings > Security > Roles`.

To create a role, just click on the `+` button:

![Create role](assets/create-role.png)

Then you will be able to define the capabilities of the role:

![Update role](assets/update-role.png)

## Users

You can manage the users in `Settings > Security > Users`. If you are using [Single-Sign-On (SSO)](../deployment/authentication.md), the users in OpenCTI are automatically created upon login.

To create a user, just click on the `+` button:

![Create user](assets/create-user-new.png)

### Manage a user

When access to a user, it is possible to:

* Visualize information including the token
* Modify it, reset 2FA if necessary
* Manage its sessions
* Manage its triggers and digests
* Visualize the history and operations
* Manage its max confidence level (override the value inherited from the group)

![User overview](assets/user-overveiw-new.png)

In the image below, you can see how to override the value inherited from the group.

![manage user](assets/user-manage.png)

!!! warning "Mandatory max confidence level"

    A user without Max confidence level won't have the ability to create, delete or update any data in our platform. Please be sure that your users are always either assigned to group that have a confidence level defined or that have an override of this group confidence level.

## Groups

Groups are the main way to manage permissions and [data segregation](segregation.md) as well as platform customization for the given users part of this group. You can manage the groups in `Settings > Security > Groups`.

Here is the description of the group available parameters.

| Parameter              | Description                                                                                                                                                               |
|:-----------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `Auto new markings`    | If a new marking definition is created, this group will automatically be granted to it.                                                                                   |
| `Default membership`   | If a new user is created (manually or upon SSO), it will be added to this group.                                                                                          |
| `Roles`                | Roles and capabilities granted to the users belonging to this group.                                                                                                      |
| `Default dashboard`    | Customize the home dashboard for the users belonging to this group.                                                                                                       |
| `Default markings`     | In `Settings > Customization > Entity types`, if a default marking definition is enabled, default markings of the group is used.                                          |
| `Allowed markings`     | Grant access to the group to the defined marking definitions, more details in [data segregation](segregation.md).                                                         |
| `Triggers and digests` | Define defaults triggers and digests for the users belonging to this group.                                                                                               |
| `Max confidence level` | Define the maximum confidence level for the group: it will impact the capacity to update entities, the confidence level of a newly created entity by a user of the group. |

![Group overview](assets/group-overview-new.png)

!!! information "Max confidence level when a user has multiple groups"
 
    A user with multiple groups will have the **the highest confidence level** of all its groups. 
    For instance, if a user is part of group A (max confidence level = 100) and group B (max confidence level = 50), then the user max confidence level will be 100.

### Manage a group

When managing a group, you can define the members and all above configurations.

![Update a group](assets/update-group-new.png)

<a id="organizations-section"></a>
## Organizations

Users can belong to organizations, which is an additional layer of [data segregation](segregation.md) and customization.

## Organization administration

Platform administrators can promote members of an organization as "Organization administrator". This elevated role grants them the necessary capabilities to create, edit and delete users from the corresponding Organization. Additionally, administrators have the flexibility to define a list of groups that can be granted to newly created members by the organization administrators. This feature simplifies the process of granting appropriate access and privileges to individuals joining the organization.

![Organization admin Settings view](assets/organization_admin_view.png)

The platform administrator can promote/demote an organization admin through its user edition form.

![Organization admin promoting/demoting](assets/define_organization_admin.png)

!!! info "Organization admin rights"

    The "Organization admin" has restricted access to Settings. They can only manage the members of the organizations for which they have been promoted as "admins".
