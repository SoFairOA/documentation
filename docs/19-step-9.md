# Step 9: Notify SWH of the mention

## Context

**Trigger**: a mention has been identified or has been modified or cancelled 

**End result:** origin metadata in Software Heritage is updated with link to
article with software mention information, identifying authority as OA
repository + CORE.

From the paper metadata:

* `paper_id` => `referencePublication`
    * `paper_title` => `referencePublication.name`
    * `author_name` => `referencePublication.author.givenName`, `referencePublication.author.familyName`
    * `author_email` => `referencePublication.author.email`

From the Softcite metadata:

* `software_mention_context`
* `software_mention_attributes`
* `software_repository_origin_link` => `codeRepository`

## Requirements

1. Setup a COAR Notify server and client https://coar-notify.net/catalogue/code-libraries/
2. [Request an account](https://docs.softwareheritage.org/user/coarnotify/howto/account.html)
   on Software Heritage's COAR Notify Server

## 9.1 Notify Software Heritage of the mention

[Send the COAR Notification for the mention](https://docs.softwareheritage.org/user/coarnotify/howto/mention.html) 
through an authenticated `POST` request to 
[Software Heritage Inbox](https://inbox.softwareheritage.org)

```json
{
  "@context": [
    "https://www.w3.org/ns/activitystreams",
    "https://coar-notify.net"
  ],
  "actor": {
    "id": "https://open-access.repository",
    "name": "Open Access Repository",
    "type": "Organization"
  },
  "context": {
    "id": "{paper_id}",
    "sorg:name": "{paper_title}",
    "sorg:author": {
        "@type": "Person",
        "givenName": "{author_name}",
        "email": "{author_email}",
    },
    "ietf:cite-as": "https://doi.org/XXX/YYY",
    "ietf:item": {
      "id": "{paper_id}/document.pdf",
      "mediaType": "application/pdf",
      "type": [
        "Object",
        "sorg:ScholarlyArticle"
      ]
    },
    "type": [
      "Page",
      "sorg:AboutPage"
    ]
  }, 
  "id": "urn:uuid:6908e2d0-ab41-4fbf-8b27-e6d6cf1f7b95",
  "object": {
    "as:subject": "{paper_id}",
    "as:relationship": "https://w3id.org/codemeta/3.0#citation",
    "as:object": " {software_repository_origin_link}",
    "id": "urn:uuid:74FFB356-0632-44D9-B176-888DA85758DC",
    "type": "Relationship"
  },
  "origin": {
    "id": "https://core.co.uk",
    "inbox": "https://inbox.core.co.uk",
    "type": "Service"
  },
  "target": {
    "id": "https://www.softwareheritage.org",
    "inbox": "https://inbox.softwareheritage.org",
    "type": "Service"
  },
  "type": [
    "Announce",
    "coar-notify:RelationshipAction"
  ]
}
```

## 9.2 Software Heritage tentatively accepts the mention

The mention sent in step 9.1 is structurally valid, Software Heritage acknowledge its 
reception with a [Tentatively Accept pattern](https://coar-notify.net/specification/1.0.1/tentative-accept/).

Software Heritage COAR Notify server sends a Notification to the inbox indicated in the
`origin` key of the original Notification, mentioning its `id` in the `inReplyTo` key
and containing the original message (except its `@context`) in the `object`.

```json
{
  "@context": [
    "https://www.w3.org/ns/activitystreams",
    "https://coar-notify.net"
  ],
  "id": "urn:uuid:4fb3af44-d4f8-4226-9475-2d09c2d8d9e0",
  "inReplyTo": "urn:uuid:6908e2d0-ab41-4fbf-8b27-e6d6cf1f7b95",
    "object": {
    ...
  },
  "target": {
    "id": "https://core.co.uk",
    "inbox": "https://inbox.core.co.uk",
    "type": "Service"
  },
  "origin": {
    "id": "https://www.softwareheritage.org",
    "inbox": "https://inbox.softwareheritage.org",
    "type": "Service"
  },
  "summary": "The offer has been tentatively accepted, subject to further review.",
  "type": "TentativeAccept"
}
```

## 9.3 Software Heritage ingest the mention

The raw Notification is archived in the metadata storage and processed to build an
Extrinsic Metadata object attached to the `software_repository_origin_link`. This object
is then indexed by the search engine and made available through the 
[extrinsic-metadata API](https://archive.softwareheritage.org/api/1/extrinsic-metadata/origin/doc/)
endpoint.

## 9.4 Software Heritage accepts the mention

The mention sent in step 9.1 has been successfully archived.

Software Heritage COAR Notify server sends a Notification to the inbox indicated in the
`origin` key of the original Notification, mentioning its `id` in the `inReplyTo` key
and containing the original message (except its `@context`) in the `object`.

```json
{
  "@context": [
    "https://www.w3.org/ns/activitystreams",
    "https://coar-notify.net"
  ],
  "inReplyTo": "urn:uuid:6908e2d0-ab41-4fbf-8b27-e6d6cf1f7b95",
  "id": "urn:uuid:651ca751-8559-4c10-8256-92ef225ca5a1",
  "object": {
    ...
  },
  "target": {
    "id": "https://core.co.uk",
    "inbox": "https://inbox.core.co.uk",
    "type": "Service"
  },
  "origin": {
    "id": "https://www.softwareheritage.org",
    "inbox": "https://inbox.softwareheritage.org",
    "type": "Service"
  },
  "summary": "The mention has been archived by Software Heritage",
  "type": "Accept"
}
```

## 9.5 Software Heritage rejects the mention

The mention sent in step 9.1 has been rejected because: 

- the notification is structurally invalid 
- the software mentioned can't be archived
- something went wrong when ingesting the mention

Software Heritage COAR Notify server sends a Notification to the inbox indicated in the
`origin` key of the original Notification, mentioning its `id` in the `inReplyTo` key
and containing the original message (except its `@context`) in the `object` key.

The error message explaining the reason why it was rejected will be found in the
`summary` field.

```json
{
  "@context": [
    "https://www.w3.org/ns/activitystreams",
    "https://coar-notify.net"
  ],
  "inReplyTo": "urn:uuid:6908e2d0-ab41-4fbf-8b27-e6d6cf1f7b95",
  "id": "urn:uuid:4ec57878-edcf-4e54-a4a3-9c7582cb17b3",
  "object": {
    ...
  },
  "target": {
    "id": "https://core.co.uk",
    "inbox": "https://inbox.core.co.uk",
    "type": "Service"
  },
  "origin": {
    "id": "https://www.softwareheritage.org",
    "inbox": "https://inbox.softwareheritage.org",
    "type": "Service"
  },
  "summary": "Unable to archive this mention because...",
  "type": "Reject"
}
```

## 9.6 Undo previous notification sent to Software Heritage

The mention sent in step 9.1 has been rejected by the author of the article. 

Send a `Undo` COAR Notification to the Software Heritage Inbox mentioning the `id` of
Notification sent in 9.1 in the `inReplyTo` key and containing the original message
(except its `@context`) in the `object`.

```json
{
  "@context": [
    "https://www.w3.org/ns/activitystreams",
    "https://coar-notify.net"
  ],
  "inReplyTo": "urn:uuid:651ca751-8559-4c10-8256-92ef225ca5a1",
  "id": "urn:uuid:e4999c92-f9ea-4cbe-8970-41d6d00750dc",
  "object": {
    ...
  },
  "origin": {
    "id": "https://core.co.uk",
    "inbox": "https://inbox.core.co.uk",
    "type": "Service"
  },
  "target": {
    "id": "https://www.softwareheritage.org",
    "inbox": "https://inbox.softwareheritage.org",
    "type": "Service"
  },
  "summary": "The author rejected this mention",
  "type": "Undo"
}
```

This Notification is ingested by Software Heritage (see 9.3) and remove the Extrinsic 
Metadata object previously created.

## Edge cases

* URL or SWHID aren’t available
    * Rejected save code now request and rejected deposit
* An unknown failure happened while ingesting the mention

### Error cases

* The server answers with a `400` error code
   * Your payload is either malformed or invalid
* The server answers with a `401` error code
   * All API calls MUST be authenticated with the proper HTTP header.
* You receive an `UnprocessableNotification` in response to your mention
   * Software Heritage inbox url MUST match the `payload['target']['inbox']` value in 
     the notification.
  * `payload['object']['as:object']` is neither an URL nor a (Qualified) SWHID
* You receive a `Reject` notification in response to your mention
   * `context['type']` MUST contain `sorg:SoftwareSourceCode`