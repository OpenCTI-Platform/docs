# Users and RBAC
This section will provide a full overview of the OpenCTI User Account Configuration options and Role Based Access Control (RBAC) features.

## User Account Configuration Options
Internal OpenCTI user accounts are required to use the platform. These accounts can be manually created by an Administrator for use with the Local Authentication strategy or auto-provisioned using one of the other providers (LDAP, SAML, OpenID, OAuth, Google, Facebook, Github, etc).

### Basic Information

The user accounts have a number of basic fields. Most fields are editable from the Settings-->Security-->Users screen. The Token field and the 2FA State are informational and non-editable. 

![Basic Information](./assets/user_basic_information.png?raw=true "Basic Information")

The fields and their configuration options are as follows:

- Email Address - Used to send automated emails. Can be used as the username for login to the platform
- 2FA State - This describes the two-factor authentication configuration for the account. By default, this is marked as disabled. A user can  enable this off of their profile and it will reflect enabled.
- Token - This filed is non-editable and auto-assigned upon account creation. This system uses this internally to identify changes made by this particular user within the platform.
- Firstname - The user's firstname
- Lastname - The user's lastname or family surname
- Account Status - This features has (4) four possible settings:
   - Active - Default setting - account is enabled for login.
   - Inactive - This is manually set by an administrator. There is a customizable warning message handled by (`account_inactive_message` in config/default.json) for users that attempt to login and are flagged to this account status.
   - Locked - This is automatically tied to the optional `Account Expire Date` field. There is a customizable warning message handled by (`account_locked_message` in config/default.json) for users that attempt to login and are flagged to this account status.
   - Locked - Missing Training - This is to support organization processes that allow for an account to be created in the background - but require a new user to take some sort of specific trainings to be allowed access. There is a customizable warning message handled by (`account_locked_missing_training_message` in config/default.json) for users that attempt to login and are flagged to this account status.
- Account Expire Date (Optional) - This can be use to automatic move an account to `Locked` upon first attempted login after the expire date has been reached.

NOTE: Changing to the `Account Status` value on an account will result in termination of any "active" sessions. The point being, should you as an Administrator, lock or mark an account Inactive, you would not want - if that user were currently logged in - a session to persist for them after that flag was set. All sessions are terminated for a user upon Account Status change.

### User Permissions
The user accounts have a number of permissions fields. Most fields are editable from the Settings-->Security-->Users screen. The Hidden Entity Types field is informational and non-editable. 

![User Permissions](./assets/user_permissions.png?raw=true "User Permissions")

- Roles - These are the current platform roles assigned to the user. These roles impact permissions the user has within the platform.
- Groups -  These are the current groups the user is a member of and impacts various aspects of object access and capabilities within the platform.
- Organizations - These are the organizations the user is a member of and impacts various aspects of object access and capabilities within the platform.
- Sessions - This field lists active login sessions and when they will be expired/auto-logged out. You can click on the "trashcan" icon and terminate the session immediately.
- Hidden Entity Types - These are object types that have been administratively hidden within the platform from this user account.


## Role Based Access Control (RBAC)
...Coming Soon...
