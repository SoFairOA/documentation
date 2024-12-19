# Step 8: Request dir SWHID (“PID”) using URL

## Context

Getting the [SoftWare Hash persistent IDentifiers](https://www.swhid.org/) of the code archive **directory**.

The Software Heritage GraphQL service allows to fetch the server data using a **query language** and enables them to create powerful requests:  https://archive.softwareheritage.org/graphql/

This mechanism offers a unique set of advantages for GraphQL over the REST APIs.

**The trigger:** OA repository request archival and registers metadata to get a PID for the metadata record.

## Actions

OA requests identifier for metadata record, using repository URL.

The identifier request response contains a property `snapshot_swhid` which gives SWHID of snapshot associated to the visit.

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

See [Step 2.3](12-step-2)

## Edge cases
