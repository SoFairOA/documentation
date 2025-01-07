# Step 9: Deposit (article) metadata record (optional - depends on OA repository)

## Context

**Trigger**: archived repository done.

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
* `software_repository_link` => `codeRepository`

## Action (deposit)

1. Register an account on
   [https://archive.softwareheritage.org/oidc/login/](https://archive.softwareheritage.org/oidc/login/).
2. Acquire credentials as deposit client
3. Prepare the metadata file for the deposit, which:
    1. Is composed of ONLY one Atom XML document.
    2. MUST comply with the metadata requirements:
        1. The schema/vocabulary used MUST be specified with a persistent url
           (DublinCore, DOAP, CodeMeta, etc.).
        2. The name of the software deposit MUST be provided [`atom:title`,
           `codemeta:name`, `dcterms:title`].
        3. The authors of the software deposit MUST be provided.
        4. The url representing the location of the source MAY be provided
           under the url tag. The url will be used for creating an origin
           object in the archive.
        5. The `create_origin` tag SHOULD be used to specify the URL of the
           origin to create (otherwise, a fallback is created using the slug,
           or a random string if missing).
        6. The description of the software deposit SHOULD be
           provided [`codemeta:description`]: short or long description of the
           software.
        7. The license/s of the software deposit SHOULD be provided
           [`codemeta:license`].
        8. Other metadata MAY be added with terms defined by the schema in use.
    3. MUST reference the origin in a deposit tag.
    4. Reference SHOULD exist in the SWH archive.
4. Push the metadata-only deposit through an authenticated (username/password)
   [POST
   request](https://docs.softwareheritage.org/devel/swh-deposit/endpoints/collection.html#api-create-deposit)
   to `https://deposit.softwareheritage.org/1/[client_name]/` with Atom file as
   form data.

```xml
<?xml version="1.0"?>
<entry xmlns="http://www.w3.org/2005/Atom"
       xmlns:codemeta="https://doi.org/10.5063/SCHEMA/CODEMETA-2.0"
       xmlns:swh="https://www.softwareheritage.org/schema/2018/deposit">
  <author>
    <name>HAL</name>
    <email>hal@ccsd.cnrs.fr</email>
  </author>
  <codemeta:name>The assignment problem</codemeta:name>
  <codemeta:url>https://hal.archives-ouvertes.fr/hal-01243573</codemeta:url>
  <codemeta:identifier>other identifier, DOI, ARK</codemeta:identifier>
  <codemeta:applicationCategory>Domain</codemeta:applicationCategory>
  <codemeta:description>description</codemeta:description>
  <codemeta:author>
    <codemeta:name>Author1</codemeta:name>
    <codemeta:affiliation>Inria</codemeta:affiliation>
    <codemeta:affiliation>UPMC</codemeta:affiliation>
  </codemeta:author>
  <codemeta:author>
    <codemeta:name>Author2</codemeta:name>
    <codemeta:affiliation>Inria</codemeta:affiliation>
    <codemeta:affiliation>UPMC</codemeta:affiliation>
  </codemeta:author>
  <swh:deposit>
    <swh:reference>
      <swh:origin url='https://github.com/user/repo'/>
    </swh:reference>
  </swh:deposit>
</entry>
```

*Example using Atom with CodeMeta*

```bash
curl -i -u hal:<pass> \
     -F "atom=@atom-entry.xml;type=application/atom+xml;charset=UTF-8" \
     -H 'In-Progress: false' \
     -XPOST https://deposit.softwareheritage.org/1/hal/
```
*Example of POST request*
