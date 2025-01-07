# Workflow

This documentation will assist service providers, mainly OA repositories, to
provide a new service to authors that want to seamlessly connect their articles
with an archived instance of their software source code.

## The use cases

UC1: A researcher references software in their article and would like to have
tools to easily link and archive their software output on the metadata record

UC2: An OA repository manager wants to provide a software reference service to
their users with the assistance of CORE’s harvest

These use cases are aligned with the Open Science vision to create
interoperability in the scholarly ecosystem and provide tools and
infrastructure for reproducible research.

## The sequence diagrams

### The full workflow sequence diagram

The sequence diagram below is the nominal scenario of the full workflow.

```mermaid
sequenceDiagram
    Participant Research team
    Participant Forge
    Participant OA Repository
    Participant CORE
    Participant SWH
    Research team->>Forge: 1. share sw on forge's repo
    Research team->>OA Repository:  2. deposit manuscript
    opt moderation
    alt revision required
######         OA Repository-->> Research team: 2.1.1 request for modification
        Research team->> OA Repository: 2.1.2 provide response

    else rejection
    OA Repository-->>Research team: 2.2 notify reasons for rejection
    end
    end
    OA Repository->>Research team: 2.1.3 notify publication
    OA Repository->>OA Repository: 2.3 expose article metadata in OAI-PMH
    CORE->>OA Repository: 3.1 harvest metadata (OAI-PMH)
    OA Repository-->>CORE: Dublin Core metadata
    CORE->>CORE: 3.2 extract software mention (detailed in CORE flow sequence)

    CORE->>OA Repository: 4. notify validation request
    OA Repository->>Research team: 5.1 send mention validation request
    alt full-acceptance
        Research team-->>OA Repository: 5.2.1 accept mentions
        OA Repository-->>CORE: 5.2.1.1 send acceptance
        CORE->>CORE: 5.2.1.2 mark mention as validated
    else if partial acceptance (with rectifications)
        Research team-->>OA Repository: 5.2.2 rectify mentions on form
        OA Repository->>OA Repository: 5.2.2.1 update record in OA
        OA Repository-->>CORE: 5.2.2.1 send new properties
        CORE->>CORE: 5.2.2.2 mark changes
    else reject or no-reply
        Research team-->>OA Repository: 5.2.3 reject mentions
        OA Repository-->>CORE: 5.2.3.1 send rejection
        CORE->>CORE: 5.2.3.2 mark mention as rejected
    end


    OA Repository->>SWH: 6 trigger software archive using API
    SWH-->>Forge: 7. visit code repo and capture last snapshot

    OA Repository->>SWH: 8.1 request dir SWHID using URL
    SWH-->>OA Repository: return SWHID
    OA Repository-->>Research team: 8.2 Show SWHID on article record
    OA Repository-->>OA Repository: 8.3 Add SWHID in Dublin Core metadata
    OA Repository->>SWH: (optional) 9 deposit metadata related to URL
```

A research team develops software in a software forge (e.g. GitHub, Gitlab,
etc.) using a dedicated code repository. This software is related to the
article written by the research team.

The research team, represented by the corresponding author or the deposit
contributor, deposits the manuscript and its associated metadata into one or
more Open Access (OA) repositories, possibly triggering a moderation process to
ensure uniqueness and cohesion.

If successful, OA repository metadata is later indexed by CORE, using OAI-PMH
protocol.

The metadata and manuscript is processed by the CORE system, including the
CHARS component, the CORE File System servers and the ML model component which
returns a TEI/XML file with mentions.

CORE will push the TEI/XML and full text to the validation module. The
validation module sends validation request to OA repository which in turn will
notify the corresponding author, representing the research team.

The corresponding author will have the possibility to validate the request.

If validated, the OA repository will proceed in the archival of the software
through Software Heritage and will expose the appropriate SWHID.

### The CORE components sequence diagram

**Pre-conditions:** repository manager accepts notifications about mentions.
The acceptance is possible using the CORE repository dashboard.

The sequence diagram below is specific to the different components necessary in
the CORE service backend.

```mermaid
sequenceDiagram

    Participant Research team
    Participant OA repository
    Participant Validation module
    Participant CHARS
    Participant CORE File Servers
    Participant ML model

    OA repository->>OA repository:  2.3 exposes Dublin Core metadata for OAI-PMH protocol
    CHARS->>OA repository: 3.1 indexes OAI-PMH metadata
    alt manuscript link exposed
        CHARS->>OA repository:  3.2.1 fetch manuscript corpus periodically
        CHARS->>CORE File Servers:  archives metadata and full text
    CORE File Servers->>ML model: send full text for processing
    ML model->>ML model: processing full text to TEI/XML
    ML model->>CORE File Servers: push TEI/XML data
    CORE File Servers->>Validation module: push TEI/XML with paper metadata record

    Validation module->>OA repository: validation request
    OA repository->>Research team: request for validation

    end
```
