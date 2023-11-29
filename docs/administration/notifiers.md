# Notifiers

!!! tip "Under construction"

    We are doing our best to complete this page. 
    If you want to participate, don't hesitate to join the [Filigran Community on Slack](https://community.filigran.io) 
    or submit your pull request on the [Github doc repository](https://github.com/OpenCTI-Platform/docs).


## Data from the "User guide > notification" page to be migrated here

OpenCTI as some built-in notifier connectors that can be used to create custom notifier for notification and activity alerting. OpenCTI provides 3 built-in connectors: a webhook connector, a simplified email connector and a platform mailer connector. Connectors are registered with a schema describing how the connector will interact. For example, the webhook connector has the following schema:

- Verb: Specifies the HTTP method (GET, POST, PUT, DELETE).
- URL: Defines the destination URL for the webhook.
- Template: Specifies the message template for the notification.
- Parameters and Headers: Customizable parameters and headers sent through the webhook request.

By default, OpenCTI also provides 2 sample notifiers to communicate to Teams through a webhook.
