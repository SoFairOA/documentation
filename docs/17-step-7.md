# Step 7: Visit code repo and capture last snapshot

## Context

Receiving a save request and saving the code (i.e. creating a save task).

**The trigger:** save code task received by OA.

**End result:** Saved repo in SWH.

## Action

Once the save request has been accepted (e.g. origin URL comes from a known
code hosting provider), it creates a saving task which status can be checked
via an API GET request to the URL returned within the previous save request
response object `request_url` property or to
`https://archive.softwareheritage.org/api/1/origin/save/[request_id]/` where
path parameter `request_id` can also be found in the previous save request
response object `id` property.

## Edge cases

* Acceptance and task creation depends on the provided origin URL; returns
  `400` or `403` if an invalid (e.g. missing) or blacklisted URL has been
  provided.

Check property `save_request_status`, which can be accepted, rejected or
pending (pending needs manual check) and `notes` to get more information if
rejected.

* `404`: no save request found.
