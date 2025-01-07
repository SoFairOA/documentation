# Step 4: Notify validation request

## Context

**Pre-conditions:** repository manager accepts notifications about mentions,
using the CORE repository dashboard.

**The trigger:** A mention was identified with or without an url to a code
repository. With this trigger the validation process will begin to get the
author’s approval. The approval is about the connection between the article and
identified software mentioned.

**End result:** article id, software url or name sent to OA.

## Action: Integration of notifications with repository platforms (T6.3)

The notification sent to the repository will contain the following information
about a paper that has been analysed as containing software mentions;

From the paper metadata:

* `paper_id`
* `paper_title`

From the Softcite metadata:

* `software_mention_context`
* `software_mention_attributes`
* `software_repository_link`


## CORE Dashboard - new notifications tab / module (T6.1)

For the management of notifications, CORE will develop a new module for its
existing repository dashboard. The repository manager will need to enable
notifications and provide the endpoint URL to which notifications should be
sent.

OA MUST have credentials to connect to the dashboard and enable notifications.

![CORE Dashboard Module](img/CORE-dashboard-module.png)

*Figure 1:  CORE Dashboard module*

Once the software mentions are generated, there will be two modes of operation:
- automatic, all the software mentions (over a certain confidence threshold)
  automatically detected will be sent to the authors for validation.
- manual, the repository manager can review and select which mentions are valid
  or ask for which mentions to be sent to the authors for validation.

The mentions that require validation will be propagated to an endpoint hosted
internally by the Institutional Repository and configured using the CORE
Dashboard settings.

The endpoint will have a standard authentication system (to be agreed between
CORE and the Institutional Repository) and the format will be compliant with
the [COAR-Notify](https://coar-notify.net/) protocol.

The payload of the message will be as follow:

```
{
 "@context": [
   "https://www.w3.org/ns/activitystreams",
   "https://purl.org/coar/notify"
 ],
 "actor": {
   "id": "mailto:library@repo.com",
   "name": "Repository manager",
   "type": "Person"
 },
 "id": "urn:uuid:0370c0fb-bb78-4a9b-87f5-bed307a509dd",
 "object": {
   "id": "https://research-organisation.org/repository/record/201203/421/", //identifier of paper
   "ietf:cite-as": "https://doi.org/10.5555/12345680", //PID of paper
   "sorg:citation":{
       "@context": "https://doi.org/10.5063/schema/codemeta-2.0",
       "type": "SoftwareSourceCode",
       "name": "SoFAIR",
       "referencePublication": "https://doi.org/10.1016/j.procs.2012.04.202"
   },
   "mentionConfidence":99.0,
   "mentionType":"used",
   "mentionContext": "In this paper, we present the software X vY (http://sw/link)"
 },
 "origin": {
   "id": "https://research-organisation.org/repository",
   "inbox": "https://research-organisation.org/inbox/",
   "type": "Service"
 },
 "target": {
   "id": "https://review-service.com/system", // CORE
   "inbox": "https://review-service.com/inbox/", // CORE edit/input form
   "type": "Service"
 },
 "type": [
   "Offer",
   "coar-notify:ReviewAction"
 ]
}
```

In particular:

- `actor`: it's the entity that started the validation process, in this case
      the repository manager that requested the software mention validation.

- `id`: a unique id identifying the activity.

- `object`: will contain the software mention validation request, in practice,
      it will include the link between the paper and the software mention.
    - `id`: the identifier or the record in the repository for which the mention
          has been discovered.
    - `ietf:cite-as`: additional identifiers for the research papers, such as
          DOI, ARK or handle identifiers.
    - `sorg:citation` a description of the software using the [codemeta
          standard](https://codemeta.github.io/).
    - `mentionConfidence` the score for which the machine learning tool
          detected the citation. A higher score implies an higher degree of
          confidence that the mention is correctly identified.
    - `mentionType` the type of usage of the software detected in the paper. It
          could be, used, created, cited.
    - `mentionContext` the snippet of text where the mention was detected.

- `origin`: the details of the repository endpoint for which the mention has
      been directed.

- `target`: the details of where the results of the activity should be
      completed. In practice this should include the details to where to send
      the validation results. Most likely a CORE endpoint that would support
      both the editing and the confirmation.
