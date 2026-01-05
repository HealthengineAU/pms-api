# Healthengine Practice Management Software (PMS) API

> :warning: **PLEASE NOTE**  
While our PMS API documentation is publicly available, access to it is subject to restrictions and undergoes individual review. Before initiating any development work, please contact us at integrations@healthengine.com.au for further assistance.

The Healthengine Practice Management Software (PMS) API is designed to enable authorised third-party developers to create integrations that facilitate data synchronisation between Healthengine and their software solutions.

This integration serves the purpose of displaying bookable resources and their availability on the Healthengine booking platform.

Our [API definition](openapi.yaml) is formatted in OpenAPI v3 and can be viewed [here](https://healthengineau.github.io/pms-api/).

Following your initial review of our API definition, we strongly recommend referring to our [quick start guide](docs/quick-start-guide.md) . This guide outlines the necessary steps to establish a functional integration and the subsequent procedures for going live once the development phase is concluded.

For convenience, this [Postman Collection](postman_collection.json) is also provided and can be used to call API directly after importing collection in [Postman](https://www.postman.com/downloads/) and setting variables with credentials provided by Healthengine for testing.

Additionally, the [suggested testing document](docs/suggested-testing.md) outlines common and edge use cases that your integration should be designed to accommodate throughout the development process. Failing to provide solutions to these tests could result in your integration not being approved for deployment.

If have any questions related to this documentation, please reach out to integrations@healthengine.com.au

## Whitelist IPs

Healthengine requests such as webhooks will come from the following IP addresses:

- 13.55.48.1
- 52.62.53.70
- 13.54.122.64
