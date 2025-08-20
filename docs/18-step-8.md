# Step 8: Request dir SWHID (“PID”) using URL

## Context

Getting the [SoftWare Hash IDentifiers](https://www.swhid.org/) of the code archive
**directory**.

The [Software Heritage GraphQL service](https://archive.softwareheritage.org/graphql/)
allows to fetch the server data using a **query language** and enables them to create
powerful requests.

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

Once you found the `SWHID` matching the repository URL you can display it on the article
record using a badge:

<a href="https://archive.softwareheritage.org/swh:1:dir:bc7ddd62cf3d72ffdc365e1bf2dea6eeaa44e185;origin=https://github.com/rdicosmo/parmap;visit=swh:1:snp:8ddca416836fbbc2a7704c69db38739bef6b6cae;anchor=swh:1:rev:ecd3744ed558da4ea2bf9eb87b80b8949f417126">
    <img src="https://archive.softwareheritage.org/badge/swh:1:dir:bc7ddd62cf3d72ffdc365e1bf2dea6eeaa44e185/" alt="Archived | swh:1:dir:bc7ddd62cf3d72ffdc365e1bf2dea6eeaa44e185"/>
</a>

```html
<a href="https://archive.softwareheritage.org/swh:1:dir:bc7ddd62cf3d72ffdc365e1bf2dea6eeaa44e185;origin=https://github.com/rdicosmo/parmap;visit=swh:1:snp:8ddca416836fbbc2a7704c69db38739bef6b6cae;anchor=swh:1:rev:ecd3744ed558da4ea2bf9eb87b80b8949f417126">
    <img src="https://archive.softwareheritage.org/badge/swh:1:dir:bc7ddd62cf3d72ffdc365e1bf2dea6eeaa44e185/" alt="Archived | swh:1:dir:bc7ddd62cf3d72ffdc365e1bf2dea6eeaa44e185"/>
</a>
```

or an iframe:

<iframe style="width: 100%; height: 500px; border: 1px solid rgba(0, 0, 0, 0.125);"
        src="https://archive.softwareheritage.org/browse/embed/swh:1:dir:bc7ddd62cf3d72ffdc365e1bf2dea6eeaa44e185;origin=https://github.com/rdicosmo/parmap;visit=swh:1:snp:8ddca416836fbbc2a7704c69db38739bef6b6cae;anchor=swh:1:rev:ecd3744ed558da4ea2bf9eb87b80b8949f417126/">
</iframe>

```html
<iframe style="width: 100%; height: 500px; border: 1px solid rgba(0, 0, 0, 0.125);"
        src="https://archive.softwareheritage.org/browse/embed/swh:1:dir:bc7ddd62cf3d72ffdc365e1bf2dea6eeaa44e185;origin=https://github.com/rdicosmo/parmap;visit=swh:1:snp:8ddca416836fbbc2a7704c69db38739bef6b6cae;anchor=swh:1:rev:ecd3744ed558da4ea2bf9eb87b80b8949f417126/">
</iframe>
```

### 8.3 Add SWHID in Dublin Core metadata

See [Step 2.3](12-step-2.md)

## Edge cases
