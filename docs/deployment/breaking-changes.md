# Breaking changes and migrations

This section lists breaking changes introduced in OpenCTI, per version starting with the latest.

Please follow the migration guides if you need to upgrade your platform. 

## Breakdown per version

This table regroups all the breaking changes introduced, with the corresponding version in which the change was implemented.

| Change                                                           | Deprecated in | Changed in |
|:-----------------------------------------------------------------|:--------------|:-----------|
| [Promote Observable API](#change-to-the-observable-promote-API)  | 6.2           | 6.5        |
| [SAML authentication parameters](#change-to-SAML-authentication) |               | 6.2        |
| [Major changes to Filtering API](#new-filtering-API)             |               | 5.12       |


## OpenCTI 6.2

### Deprecation

<a id="change-to-the-observable-promote-API"></a>
#### Change to the observable promote API  

The API calls that promote an Observable to Indicator now return the created Indicator instead of the original Observable.

For more details, see [this migration guide](./breaking-changes/6.2-promote-to-indicator.md).

### Breaking Changes

<a id="change-to-SAML-authentication"></a>
### Change to SAML authentication

Upgrading `passport-saml` library introduced a breaking change with respect to the default SAML parameters regarding signing responses and assertions. 

For more details, see [this migration guide](./breaking-changes/6.2-saml-authentication.md).

#### How to migrate

If you have issues after upgrade, you can try with both parameters set to `false`.

## OpenCTI 5.12

### Breaking changes

<a id="new-filtering-API"></a>
#### Major changes to the filtering API

OpenCTI 5.12 introduces a major rework of the **filter engine** with breaking changes to the model.
A [dedicated blog post](https://blog.filigran.io/introducing-advanced-filtering-possibilities-in-opencti-552147565faf) describes the reasons behind these changes.

Please read the dedicated [migration guide](./breaking-changes/5.12-filters.md).
