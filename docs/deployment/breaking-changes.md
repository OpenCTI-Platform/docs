# Breaking changes and migrations

This section lists breaking changes introduced in OpenCTI, per version starting with the latest.

Please follow the migration guides if you need to upgrade your platform. 

## OpenCTI 6.3

### Adding of built-in retention rules on files and workbenches

After a migration to 6.3, existing platforms will have 2 new retention rules and every new platform will be created with 2 built-in retention rules. These rules can be updated and deleted.

These 2 rules aims to prevent a large amount of unused files and workbenches in Data > Import.

Here are some details about the added rules:
- a retention rule on files (scope = file, maximum retention days = 133): all the global files (i.e. files uploaded in Data > Import) correctly uploaded and whose imports are all correctly completed will be deleted if their upload occurred more than 133 days ago.
- a retention rule on workbenches (scope = workbench, maximum retention days = 60): all the global workbenches (i.e. workbenches in Data > Import) will be deleted if they haven't been modified for more than 60 days.

!!! warning "Files permanent deletion"

    After the migration, the global files uploaded for more than 133 days and the global workbenches not updated for more than 60 days will be permanently deleted.


## OpenCTI 6.2

### Change to the observable "promote"  

The API calls that promote an Observable to Indicator now return the created Indicator instead of the original Observable.

**GraphQL API**

* Mutation `StixCyberObservableEditMutations.promote` is now deprecated
* New Mutation `StixCyberObservableEditMutations.promoteToIndicator` introduced


**Client-Python API**

* Client-python method `client.stix_cyber_observable.promote_to_indicator` is now deprecated
* New Client-python method `client.stix_cyber_observable.promote_to_indicator_v2` introduced


!!! warning "Discontinued Support"

    Please note that the deprecated methods will be permanently removed in OpenCTI 6.5.

#### How to migrate

If you are using custom scripts that make use of the deprecated API methods, please update these scripts.

The changes are straightforward: if you are using the return value of the method, you should now expect the new Indicator 
instead of the Observable being promoted; adapt your code accordingly.


### Change to SAML authentication

When `want_assertions_signed` and `want_authn_response_signed` SAML parameter are not present in OpenCTI configuration, 
the default is now set to `true` by the underlying library (passport-saml) when previously it was `false` by default.

#### How to migrate

If you have issues after upgrade, you can try with both parameters set to `false`.

## OpenCTI 5.12

### Major changes to the filtering APi

OpenCTI 5.12 introduces a major rework of the **filter engine** with breaking changes to the model.

A [dedicated blog post](https://blog.filigran.io/introducing-advanced-filtering-possibilities-in-opencti-552147565faf) describes the reasons behind these changes.

#### How to migrate

Please read the dedicated [migration guide](../reference/filters-migration.md).
