!!! tip "Filigran"

    [Filigran](https://filigran.io) is providing an [Enterprise Edition](https://filigran.io/offering/subscribe) of the platform, whether [on-premise](https://filigran.io/offering/subscribe) or in the [SaaS](https://filigran.io/offering/subscribe-to-cloud).

## What is OpenCTI EE?
OpenCTI Enterprise Edition is based on the open core concept. This means that the source code of OCTI EE remains open source and included in the main GitHub repository of the platform but is published under a specific license. As specified in the GitHub license file:

The OpenCTI Community Edition is licensed under the Apache License, Version 2.0 (the “Apache License”).
The OpenCTI Enterprise Edition is licensed under the OpenCTI
Non-Commercial License (the “Non-Commercial License”).
The source files in this repository have a header indicating which license they are under. If no such header is provided, this means that the file is belonging to the Community Edition under the Apache License, Version 2.0.

We wrote a complete article to explain the enterprise edition, [feel free to read it to have more information](https://blog.filigran.io/progressive-rollout-of-the-opencti-enterprise-edition-why-what-and-how-1189e9d5603c)

## EE Activation
Enterprise edition is easy to activate. You need to go the platform settings and click on the Activate button.

![OpenCTI activation](assets/enterprise-activate.png)

Then you will need to agree to the Filigran EULA. 

![OpenCTI EE EULA](assets/enterprise-eula.png)

As a reminder:

- OpenCTI EE is free-to-use for development, testing and research purposes as well as for non-profit organizations.
- OpenCTI EE is included for all Filigran SaaS customers without additional fee.
- **For all other usages, OpenCTI EE is reserved to organizations that have signed a Filigran Enterprise agreement.**


## Available features

### Activity monitoring

Audit logs help you answer "who did what, where, and when?" within your data with the maximum level of transparency. Please read [Activity monitoring page](audit/overview.md) to get all information.

### Playbooks and automation

OpenCTI playbooks are flexible automation scenarios which can be fully customized and enabled by platform administrators to enrich, filter and modify the data created or updated in the platform. Please read [Playbook automation page](../usage/automation.md) to get all information.

### Organizations management and segregation

Organizations segregation is a way to segregate your data considering the organization associated to the users. Useful when your platform aims to share data to multiple organizations that have access to the same OpenCTI platform. Please read [Organizations RBAC](../administration/organization-segregation.md) to get more information.

### Full text indexing

Full text indexing grants improved searches across structured and unstructured data. OpenCTI classic searches are based on metadata fields (e.g. title, description, type) while advanced indexing capability  enables  searches  to  be  extended  to  the document’s contents. Please read [File indexing](../administration/file-indexing.md) to get all information.

## More to come

More features will be available in OpenCTI in the future. Features like:

- Generative AI for correlation and content generation.
- Supervised machine learning for natural language processing.
