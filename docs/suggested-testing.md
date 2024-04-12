# Suggested Testing

## Introduction

After gaining access to our test environment, a user of the Healthengine PMS API should proceed to develop an integration that facilitates the Healthengine booking workflow.

To ensure the seamless functioning of this integration, we strongly advise conducting the following minimal set of tests prior to going live.

Neglecting to satisfy these tests could potentially lead to your integration not being approved during the go-live process.

---

## Linking a PMS account to one or more Healthengine practices

Once a practice using Healthengine has granted consent for you to access their data via the Healthengine Practice Admin portal or by contacting Healthengine support, you should establish a method for the practice to share their Healthengine practice ID and practice key with you. These credentials are essential for initiating the data synchronisation process with Healthengine.

**Scenario: Linking the PMS account with a Healthengine practice**  
**Given** the presence of a PMS account  
**When** a Healthengine practice ID and practice key are supplied  
**Then** an effort to transmit practice configuration to Healthengine must be initiated

**Scenario: Practice provides Healthengine practice ID and practice key without consent or for a revoked consent**  
**Given**  a PMS account in which a practice ID and practice key have been provided which do not belong to a non-revoked consent for your consumer  
**When** a 403 Unauthorized response is received upon attempting to transmit practice configuration  
**Then** the PMS user must be informed and guided to establish consent within Healthengine

**Scenario: Integration of PMS Account with multi-location support**  
**Given** a PMS account capable of accommodating multiple PMS locations  
**When** these locations are represented as distinct Healthengine practices  
**Then**  it should be feasible to input multiple Healthengine practice ID and practice key combinations and associate each one with one or more PMS locations

**Scenario: Updating a Healthengine practice ID for a PMS account or location**  
**Given** a PMS account or location with a previously assigned Healthengine practice ID and practice key for integration purposes  
**When** the Healthengine practice ID and practice key are updated  
**Then** practice configuration must be established for the new Healthengine practice, and data transmission should cease for the previous Healthengine practice

---

## PMS account/location configuration synchronisation

Upon the establishment of a PMS account, or a PMS location in a multi-location-per-account setup, the following configuration must be provided before initiating the synchronisation of resource availability and bookings:

* A liveness contact, to inform us when to expect your integration to be responsive to requests for this practice
* A snapshot of PMS resources (practitioners or rooms)
* A snapshot of PMS appointment types

It is important to note that for snapshot endpoints, the absence of an item in the snapshot will be interpreted as a deletion in the PMS.

**Scenario: Initial configuration synchronisation**  
**Given** the presence of a PMS account or location  
**When** a new Healthengine practice ID and practice key are supplied  
**Then** the following components must be provided: a liveness contract, a resource snapshot, and an appointment type snapshot

**Scenario: Updating liveness contract times in the PMS**  
**Given** an integrated PMS account or location  
**When** there are updates to the liveness contract days or times  
**Then** the revised liveness contract details should be promptly communicated to Healthengine

**Scenario: Changes detected in PMS resource snapshot**  
**Given** an integrated PMS account or location  
**When** an existing PMS resource is altered, removed, or a new PMS resource is introduced  
**Then** an updated PMS resource snapshot should be promptly communicated to Healthengine

**Scenario: Changes detected in PMS appointment type snapshot**  
**Given** an integrated PMS account or location  
**When** an existing PMS appointment type is altered, removed, or a new PMS appointment type is introduced  
**Then** an updated PMS appointment type snapshot should be promptly communicated to Healthengine

**Scenario: Manual configuration resync action by PMS user**  
**Given** an integrated PMS account or location  
**When** a PMS user opts to manually resync the PMS account or location configuration to Healthengine  
**Then** there should be a solution available to them to do so

**Scenario: Sending configuration after being temporarily disabled**  
**Given** an integrated PMS account or location in a temporarily disabled state due to configuration in your software  
**When** a PMS user re-enables this integration  
**Then** configuration should be sent to Healthengine

---

## Handling PMS resource edge cases

Please consider these additional edge cases related to PMS resources when refining your integration logic.

**Scenario: Managing PMS practitioners with multiple specialties**  
**Given** the potential for a single practitioner to possess multiple specialties within a PMS  
**When** sending a PMS resource snapshot to Healthengine  
**Then** distinct PMS resources should be sent for each practitioner/specialty combination

---

## Syncing resource availability

Availability snapshots are expected to be sent to us without an incoming request for each PMS resource multiplied by the number of days that you are syncing into the future.

We will only request an availability snapshot from you if we have attempted to insert a booking and the request has been rejected, indicating that we might have stale availability for the resource and date. 

Resource availability should be sent to us whenever the availability has been changed in your software for a resource and date. Some examples include:
- Changing a resource's available hours
- Creating a non-Healthengine booking for the resource
- The integration being re-enabled after being disabled via configuration in your software

There should also be a sync periodically to avoid any missed updates and a manual resync option available to PMS users.

**Scenario: Manual availability resync action by PMS user**  
**Given** an integrated PMS account or location  
**When** a PMS user opts to manually resync availability to Healthengine  
**Then** there should be a solution available to them to sync availability for all resources for the future sync period

**Scenario: Sending availability after being temporarily disabled**  
**Given** an integrated PMS account or location in a temporarily disabled state due to configuration in your software   
**When** a PMS user re-enables this integration  
**Then** availability for all resources should be sent to Healthengine

---

## Handling incoming requests

Managing incoming requests from Healthengine is of utmost importance. Swift handling of these requests is essential to minimise the risk of scheduling conflicts originating from sources other than Healthengine.

Certain types of requests hold a higher priority in terms of integration health. Failure to address these critical requests within a ten minute window during the operational hours of the integration outlined in the liveness contract will result in the integration being marked as unhealthy. Consequently, the practice's appointments will be removed from publication.

**Scenario: Polling for new requests**  
**Given** an integrated PMS account or location  
**When** within the hours of the liveness contract  
**Then** regular polling of the requests endpoint for new requests from Healthengine should be performed

**Scenario: Handling priority requests**  
**Given** an integrated PMS account or location  
**When** an incoming booking, bookingUpdate or availability request is created  
**Then** the integration should resolve this request within ten minutes

---

## Handling incoming requests via webhook subscriptions

If you have established webhook subscriptions for specific Healthengine request types, any events related to those request types within Healthengine will trigger the sending of payloads to your designated webhook URL.

We strongly advise that you continue to periodically check the requests endpoint for new requests, even if webhook subscriptions are in place. This precaution is advisable in case temporary network issues or other unforeseen circumstances prevent the successful delivery of subscription payloads.

**Scenario: Acknowledging a webhook payload to prevent timeout**  
**Given** that a webhook subscription has been established for a specific request type  
**When** a webhook payload is received from Healthengine  
**Then** receipt should be acknowledged within a five second window to prevent the attempt from timing out

**Scenario: Resolving a webhook payload attempt before the maximum number of retries**  
**Given** that a webhook subscription has been established for a specific request type   
**When** a webhook payload is received from Healthengine  
**Then** receipt should be acknowledged within three attempts to avoid the request being missed

**Scenario: Handling missed webhook payloads**  
**Given** that a webhook subscription has been established for a specific request type  
**When** a webhook payload is missed because the maximum number of unacknowledged requests is reached  
**Then** polling the requests endpoint at least once an hour should be done to capture any missed requests

---

## Handling incoming booking related request edge cases

Please consider these additional edge cases related to booking requests when refining your integration logic.

**Scenario: Handling multiple request types with priority**  
**Given** the presence of unresolved booking requests alongside various booking-related requests (booking updates, PMS appointment changes, document requests/uploads) associated with an integrated PMS account or location   
**When** polling the PMS API for new requests  
**Then** the integration should prioritise the processing of booking requests to prevent processing request types that depend on the existence of a booking

**Scenario: Handling an unknown booking ID for incoming booking-related requests**  
**Given** the integration has recently been set up for a PMS account or location  
**When** an incoming booking-related request contains a booking ID that is unrecognised by the PMS  
**Then** the request should be rejected to maintain the overall health and integrity of the integration
