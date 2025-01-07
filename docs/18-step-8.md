# Step 8: Request dir SWHID (“PID”) using URL

## Context

Getting the [SoftWare Hash persistent IDentifiers](https://www.swhid.org/) of
the code archive **directory**.

The Software Heritage GraphQL service allows to fetch the server data using a
**query language** and enables them to create powerful requests:
https://archive.softwareheritage.org/graphql/

This mechanism offers a unique set of advantages for GraphQL over the REST
APIs.

**The trigger:** OA repository request archival and registers metadata to get a
PID for the metadata record.

## Actions

OA requests identifier for metadata record, using repository URL.

The identifier request response contains a property `snapshot_swhid` which
gives SWHID of snapshot associated to the visit.

### 8.1 Get SWHID from URL

```graphql
query getOriginDict {
   origin(url: "https://svn.wald.intevation.org/edbsilon") {
     url
     latestSnapshot {
       headBranch {
         target {
           node {
             ... on Release {
               target {
                 node {
                   ... on Revision {
                     directory {
                       swhid
                     }
                   }
            	    }
               }
             }
             ... on Revision {
              directory {
                swhid
              }
            }
            ... on Directory {
              swhid
            }
          }
        }
      }
    }
  }
}
```

Full documentation for GraphQL services:

* [https://docs.softwareheritage.org/devel/swh-graphql/index.html](https://docs.softwareheritage.org/devel/swh-graphql/index.html)
* [https://archive.softwareheritage.org/graphql/](https://archive.softwareheritage.org/graphql/)

### 8.2 Show SWHID on article record

### 8.3 Add SWHID in Dublin Core metadata

The current basic Dublin Core metadata standard does not specify in detail how
a software mention should be specified, however even if not explicitly the most
common metadata extension for research papers allow the definition of the
software mention using the “relation” tags.

[RIOXX](https://rioxx.net/profiles/) offers the tag
[`rioxx:ext_relation`](https://rioxx.net/profiles/#rioxxterms:ext_relation), a
possible example of a software mention should look something like this:

```
<rioxxterms:ext_relation
   rel="cite-as"
   coar_type="https://purl.org/coar/resource_type/c_5ce6">
           SWHID
</rioxxterms:ext_relation>
```

OpenAire guidelines use
[`datacite:relatedIdentifier`](https://openaire-guidelines-for-literature-repository-managers.readthedocs.io/en/v4.0.0/field_relatedidentifier.html#dci-relatedidentifier)

```
<datacite:relatedIdentifiers>
   <datacite:relatedIdentifier relatedIdentifierType="URL" relationType="Cites">SWHID</datacite:relatedIdentifier>
</datacite:relatedIdentifiers>
```

## Edge cases
