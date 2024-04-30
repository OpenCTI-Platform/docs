# Authentication

## Introduction

OpenCTI supports several authentication providers. If you configure multiple strategies, they will be tested **in the order** you declared them.

!!! note "Activation"

    You need to configure/activate only the authentication strategy that you really want to propose to your users.

The product proposes two kinds of authentication strategies:

- Form (asking user for a user/password),
- Buttons (click with authentication on an external system).

## Supported Strategies

Under the hood, we technically use the strategies provided by [PassportJS](http://www.passportjs.org/). We integrate a subset of the strategies available with passport. If you need more, we can integrate other strategies.

### Local users (form)

This strategy uses the OpenCTI database as a user management.

OpenCTI use this strategy as the default but its not the one we recommend for security reasons.

```json
"local": {
    "strategy": "LocalStrategy",
    "config": {
        "disabled": false
    }
}
```

!!! tip "Production deployment"

    Please use the LDAP/Auth0/OpenID/SAML strategy for production deployment.

### LDAP (form)

This strategy can be used to authenticate your user with your company LDAP and is based on [Passport - LDAPAuth](http://www.passportjs.org/packages/passport-ldapauth).

```json
"ldap": {
    "strategy": "LdapStrategy",
    "config": {
        "url": "ldaps://mydc.domain.com:686",
        "bind_dn": "cn=Administrator,cn=Users,dc=mydomain,dc=com",
        "bind_credentials": "MY_STRONG_PASSWORD",
        "search_base": "cn=Users,dc=mydomain,dc=com",
        "search_filter": "(cn={{username}})",
        "mail_attribute": "mail",
        // "account_attribute": "givenName",
        // "firstname_attribute": "cn",
        // "lastname_attribute": "cn",
        "account_attrgroup_search_filteribute": "givenName",
        "allow_self_signed": true
    }
}
```

If you would like to use LDAP groups to automatically associate LDAP groups and OpenCTI groups/organizations:

```json
"ldap": {
    "config": {
        ...
        "group_search_base": "cn=Groups,dc=mydomain,dc=com",
        "group_search_filter": "(member={{dn}})",
        "groups_management": { // To map LDAP Groups to OpenCTI Groups
            "group_attribute": "cn",
            "groups_mapping": ["LDAP_Group_1:OpenCTI_Group_1", "LDAP_Group_2:OpenCTI_Group_2", ...]
        },
        "organizations_management": { // To map LDAP Groups to OpenCTI Organizations
            "organizations_path": "cn",
            "organizations_mapping": ["LDAP_Group_1:OpenCTI_Organization_1", "LDAP_Group_2:OpenCTI_Organization_2", ...]
        }
    }
}
```

### SAML (button)

This strategy can be used to authenticate your user with your company SAML and is based on [Passport - SAML](http://www.passportjs.org/packages/passport-saml).

```json
"saml": {
    "identifier": "saml",
    "strategy": "SamlStrategy",
    "config": {
        "issuer": "mytestsaml",
        // "account_attribute": "nameID",
        // "firstname_attribute": "nameID",
        // "lastname_attribute": "nameID",
        "entry_point": "https://auth.mydomain.com/auth/realms/mydomain/protocol/saml",
        "saml_callback_url": "http://localhost:4000/auth/saml/callback",
        // "private_key": "MIIEvgIBADANBgkqhkiG9w0BAQEFAASCBKgwg...",
        "cert": "MIICmzCCAYMCBgF2Qt3X1zANBgkqhkiG9w0BAQsFADARMQ8w...",
        "logout_remote": false
    }
}
```

For the SAML strategy to work:

- The `cert` parameter is mandatory (PEM format) because it is used to validate the SAML response.
- The `private_key` (PEM format) is optional and is only required if you want to sign the SAML client request.

!!! note "Certificates"

    Be careful to put the `cert` / `private_key`  key in PEM format. Indeed, a lot of systems generally export the keys in X509 / PCKS12 formats and so you will need to convert them. 
    Here is an example to extract PEM from PCKS12:
    ```bash
    openssl pkcs12 -in keystore.p12 -out newfile.pem -nodes
    ```

Here is an example of SAML configuration using environment variables:

```yaml
- PROVIDERS__SAML__STRATEGY=SamlStrategy 
- "PROVIDERS__SAML__CONFIG__LABEL=Login with SAML"
- PROVIDERS__SAML__CONFIG__ISSUER=mydomain
- PROVIDERS__SAML__CONFIG__ENTRY_POINT=https://auth.mydomain.com/auth/realms/mydomain/protocol/saml
- PROVIDERS__SAML__CONFIG__SAML_CALLBACK_URL=http://opencti.mydomain.com/auth/saml/callback
- PROVIDERS__SAML__CONFIG__CERT=MIICmzCCAYMCBgF3Rt3X1zANBgkqhkiG9w0BAQsFADARMQ8w
- PROVIDERS__SAML__CONFIG__LOGOUT_REMOTE=false
```

OpenCTI supports mapping SAML Roles/Groups on OpenCTI Groups. Here is an example:

```json
"saml": {
    "config": {
        ...,
        // Groups mapping
        "groups_management": { // To map SAML Groups to OpenCTI Groups
            "group_attributes": ["Group"],
            "groups_mapping": ["SAML_Group_1:OpenCTI_Group_1", "SAML_Group_2:OpenCTI_Group_2", ...]
        },
        "groups_management": { // To map SAML Roles to OpenCTI Groups
            "group_attributes": ["Role"],
            "groups_mapping": ["SAML_Role_1:OpenCTI_Group_1", "SAML_Role_2:OpenCTI_Group_2", ...]
        },
        // Organizations mapping
        "organizations_management": { // To map SAML Groups to OpenCTI Organizations
            "organizations_path": ["Group"],
            "organizations_mapping": ["SAML_Group_1:OpenCTI_Organization_1", "SAML_Group_2:OpenCTI_Organization_2", ...]
        },
        "organizations_management": { // To map SAML Roles to OpenCTI Organizations
            "organizations_path": ["Role"],
            "organizations_mapping": ["SAML_Role_1:OpenCTI_Organization_1", "SAML_Role_2:OpenCTI_Organization_2", ...]
        }
    }
}
```

Here is an example of SAML Groups mapping configuration using environment variables:

```yaml
- "PROVIDERS__SAML__CONFIG__GROUPS_MANAGEMENT__GROUP_ATTRIBUTES=[\"Group\"]"
- "PROVIDERS__SAML__CONFIG__GROUPS_MANAGEMENT__GROUPS_MAPPING=[\"SAML_Group_1:OpenCTI_Group_1\", \"SAML_Group_2:OpenCTI_Group_2\", ...]"
```

### Auth0 (button)

This strategy allows to use [Auth0 Service](https://auth0.com) to handle the authentication and is based on [Passport - Auth0](http://www.passportjs.org/packages/passport-auth0).

```json
"authzero": {
    "identifier": "auth0",
    "strategy": "Auth0Strategy",
    "config": {
        "clientID": "XXXXXXXXXXXXXXXXXX",
        "baseURL": "https://opencti.mydomain.com",
        "clientSecret": "XXXXXXXXXXXXXXXXXX",
        "callback_url": "https://opencti.mydomain.com/auth/auth0/callback",
        "domain": "mycompany.eu.auth0.com",
        "audience": "XXXXXXXXXXXXXXX",
        "scope": "openid email profile XXXXXXXXXXXXXXX",
        "logout_remote": false
    }
}
```

Here is an example of Auth0 configuration using environment variables:

```yaml
- PROVIDERS__AUTHZERO__STRATEGY=Auth0Strategy
- PROVIDERS__AUTHZERO__CONFIG__CLIENT_ID=${AUTH0_CLIENT_ID}
- PROVIDERS__AUTHZERO__CONFIG__BASEURL=${AUTH0_BASE_URL}
- PROVIDERS__AUTHZERO__CONFIG__CLIENT_SECRET=${AUTH0_CLIENT_SECRET}
- PROVIDERS__AUTHZERO__CONFIG__CALLBACK_URL=${AUTH0_CALLBACK_URL}
- PROVIDERS__AUTHZERO__CONFIG__DOMAIN=${AUTH0_DOMAIN}
- "PROVIDERS__AUTHZERO__CONFIG__SCOPE=openid email profile"
- PROVIDERS__AUTHZERO__CONFIG__LOGOUT_REMOTE=false
```

### OpenID Connect (button)

This strategy allows to use the [OpenID Connect Protocol](https://openid.net/connect) to handle the authentication and is based on [Node OpenID Client](https://github.com/panva/node-openid-client) which is more powerful than the passport one.

```json
"oic": {
    "identifier": "oic",
    "strategy": "OpenIDConnectStrategy",
    "config": {
        "label": "Login with OpenID",
        "issuer": "https://auth.mydomain.com/auth/realms/mydomain",
        "client_id": "XXXXXXXXXXXXXXXXXX",
        "client_secret": "XXXXXXXXXXXXXXXXXX",
        "redirect_uris": ["https://opencti.mydomain.com/auth/oic/callback"],
        "logout_remote": false
    }
}
```

Here is an example of OpenID configuration using environment variables:

```yaml
- PROVIDERS__OPENID__STRATEGY=OpenIDConnectStrategy 
- "PROVIDERS__OPENID__CONFIG__LABEL=Login with OpenID"
- PROVIDERS__OPENID__CONFIG__ISSUER=https://auth.mydomain.com/auth/realms/xxxx
- PROVIDERS__OPENID__CONFIG__CLIENT_ID=XXXXXXXXXXXXXXXXXX
- PROVIDERS__OPENID__CONFIG__CLIENT_SECRET=XXXXXXXXXXXXXXXXXX
- "PROVIDERS__OPENID__CONFIG__REDIRECT_URIS=[\"https://opencti.mydomain.com/auth/oic/callback\"]"
- PROVIDERS__OPENID__CONFIG__LOGOUT_REMOTE=false
```

OpenCTI support mapping OpenID Claims on OpenCTI Groups (everything is tied to a group in the platform). Here is an example:

```json
"oic": {
    "config": {
        ...,
        // Groups mapping
        "groups_management": { // To map OpenID Claims to OpenCTI Groups
            "groups_scope": "groups",
            "groups_path": ["groups", "realm_access.groups", "resource_access.account.groups"],
            "groups_mapping": ["OpenID_Group_1:OpenCTI_Group_1", "OpenID_Group_2:OpenCTI_Group_2", ...]
        },
        // Organizations mapping  
        "organizations_management": { // To map OpenID Claims to OpenCTI Organizations
            "organizations_scope": "groups",
            "organizations_path": ["groups", "realm_access.groups", "resource_access.account.groups"],
            "organizations_mapping": ["OpenID_Group_1:OpenCTI_Group_1", "OpenID_Group_2:OpenCTI_Group_2", ...]
        },
    }
}
```

Here is an example of OpenID Groups mapping configuration using environment variables:

```yaml
- PROVIDERS__OPENID__CONFIG__GROUPS_MANAGEMENT__GROUPS_SCOPE=groups
- "PROVIDERS__OPENID__CONFIG__GROUPS_MANAGEMENT__GROUPS_PATH=[\"groups\", \"realm_access.groups\", \"resource_access.account.groups\"]"
- "PROVIDERS__OPENID__CONFIG__GROUPS_MANAGEMENT__GROUPS_MAPPING=[\"OpenID_Group_1:OpenCTI_Group_1\", \"OpenID_Group_2:OpenCTI_Group_2\", ...]"
```

By default, the claims are mapped based on the content of the JWT `access_token`. If you want to map claims which are in other JWT (such as `id_token`), you can define the following environment variables:

```yaml
- PROVIDERS__OPENID__CONFIG__GROUPS_MANAGEMENT__TOKEN_REFERENCE=id_token
- PROVIDERS__OPENID__CONFIG__ORGANISATIONS_MANAGEMENT__TOKEN_REFERENCE=id_token
```

Alternatively, you can request OpenCTI to use claims from the `userinfo` endpoint instead of a JWT.
```yaml
- PROVIDERS__OPENID__CONFIG__GROUPS_MANAGEMENT__READ_USERINFO=true
- PROVIDERS__OPENID__CONFIG__ORGANISATIONS_MANAGEMENT__READ_USERINFO=true
```

### Facebook (button)

This strategy can authenticate your users with Facebook and is based on [Passport - Facebook](http://www.passportjs.org/packages/passport-facebook).

```json
"facebook": {
    "identifier": "facebook",
    "strategy": "FacebookStrategy",
    "config": {
        "client_id": "XXXXXXXXXXXXXXXXXX",
        "client_secret": "XXXXXXXXXXXXXXXXXX",
        "callback_url": "https://opencti.mydomain.com/auth/facebook/callback",
        "logout_remote": false
    }
}
```

### Google (button)

This strategy can authenticate your users with Google and is based on [Passport - Google](http://www.passportjs.org/packages/passport-google-oauth).

```json
"google": {
    "identifier": "google",
    "strategy": "GoogleStrategy",
    "config": {
        "client_id": "XXXXXXXXXXXXXXXXXX",
        "client_secret": "XXXXXXXXXXXXXXXXXX",
        "callback_url": "https://opencti.mydomain.com/auth/google/callback",
        "logout_remote": false
    }
}
```

### GitHub (button)

This strategy can authenticate your users with GitHub and is based on [Passport - GitHub](http://www.passportjs.org/packages/passport-github).

```json
"github": {
    "identifier": "github",
    "strategy": "GithubStrategy",
    "config": {
        "client_id": "XXXXXXXXXXXXXXXXXX",
        "client_secret": "XXXXXXXXXXXXXXXXXX",
        "callback_url": "https://opencti.mydomain.com/auth/github/callback",
        "logout_remote": false
  }
}
```

### Client certificate (button)

This strategy can authenticate a user based on SSL client certificates. For this, you need to configure OpenCTI to start in HTTPS, for example:

```json
"port": 443,
"https_cert": {
    "key": "/cert/server_key.pem",
    "crt": "/cert/server_cert.pem",
    "reject_unauthorized": true
}
```

And then add the `ClientCertStrategy`:

```json
"cert": {
    "strategy":"ClientCertStrategy",
    "config": {
        "label":"CLIENT CERT"
    }
}
```

Afterwards, when accessing for the first time OpenCTI, the browser will ask for the certificate you want to use.

### Proxy headers (automatic)

This strategy can authenticate the users directly from trusted headers.

```json
{
  "header": {
    "strategy": "HeaderStrategy",
    "config": {
      "disabled": false,
      "header_email": "auth_email_address",
      "header_name": "auth_name",
      "header_firstname": "auth_firstname",
      "header_lastname": "auth_lastname",
      "logout_uri": "https://www.filigran.io",
      "groups_management": {
        "groups_header": "auth_groups",
        "groups_splitter": ",",
        "groups_mapping": ["admin:admin", "root:root"]
      },
      "organizations_management": {
        "organizations_header": "auth_institution",
        "organizations_splitter": ",",
        "organizations_mapping": ["test:test"]
      }
    }
  }
}
```

If this mode is activated and the headers are available, the user will be automatically logged without any action or notice. The logout uri will remove the session and redirect to the configured uri. If not specified, the redirect will be done to the request referer and so the header authentication will be done again.

## Automatically create group on SSO

The variable **auto_create_group** can be added in the options of some strategies (LDAP, SAML and OpenID). If this variable is true, the groups of a user that logins will automatically be created if they don’t exist.

More precisely, if the user that tries to authenticate has groups that don’t exist in OpenCTI but exist in the SSO configuration, there are two cases:

- if *auto_create_group= true* in the SSO configuration: the groups are created at the platform initialization and the user will be mapped on them.
- else: an error is raised.

**Example**

We assume that *Group1* exists in the platform, and *newGroup* doesn’t exist. The user that tries to log in has the group *newGroup*. If *auto_create_group = true* in the SSO configuration, the group named *newGroup* will be created at the platform initialization and the user will be mapped on it. If *auto_create_group = false* or is undefined, the user can’t log in and an error is raised.

```json
"groups_management": {
  "group_attribute": "cn",
  "groups_mapping": ["SSO_GROUP_NAME1:group1", "SSO_GROUP_NAME_2:newGroup", ...]
},
"auto_create_group": true
```

## Examples

### LDAP then fallback to local

In this example the users have a login form and need to enter login and password. The authentication is done on LDAP first, then locally if user failed to authenticate and finally fail if none of them succeeded. Here is an example for the `production.json` file:

```json
"providers": {
    "ldap": {
        "strategy": "LdapStrategy",
        "config": {
            "url": "ldaps://mydc.mydomain.com:636",
            "bind_dn": "cn=Administrator,cn=Users,dc=mydomain,dc=com",
            "bind_credentials": "MY_STRONG_PASSWORD",
            "search_base": "cn=Users,dc=mydomain,dc=com",
            "search_filter": "(cn={{username}})",
            "mail_attribute": "mail",
            "account_attribute": "givenName"
        }
    },
    "local": {
        "strategy": "LocalStrategy",
        "config": {
            "disabled": false
        }
    }
}
```

If you use a container deployment, here is an example using environment variables:

```yaml
- PROVIDERS__LDAP__STRATEGY=LdapStrategy
- PROVIDERS__LDAP__CONFIG__URL=ldaps://mydc.mydomain.org:636
- PROVIDERS__LDAP__CONFIG__BIND_DN=cn=Administrator,cn=Users,dc=mydomain,dc=com
- PROVIDERS__LDAP__CONFIG__BIND_CREDENTIALS=XXXXXXXXXX
- PROVIDERS__LDAP__CONFIG__SEARCH_BASE=cn=Users,dc=mydomain,dc=com
- PROVIDERS__LDAP__CONFIG__SEARCH_FILTER=(cn={{username}})
- PROVIDERS__LDAP__CONFIG__MAIL_ATTRIBUTE=mail
- PROVIDERS__LDAP__CONFIG__ACCOUNT_ATTRIBUTE=givenName
- PROVIDERS__LDAP__CONFIG__ALLOW_SELF_SIGNED=true
- PROVIDERS__LOCAL__STRATEGY=LocalStrategy
```
