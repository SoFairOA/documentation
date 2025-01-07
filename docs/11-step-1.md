# Step 1: Author shares software in a code repository

## Context

Research software is typically developed in forges which are primarily created
to handle the management of software engineering artefacts, including source
code, binary files, and documentation, throughout their entire lifecycle (Le
Berre at al. 2023). Software forges offer collaborative development tools for
tracking code changes, managing user tickets, contributions, and projects.
Forges are used by millions of developers worldwide to share software source
code in code repositories. These platforms facilitate collaboration among
researchers as well when it comes to create, develop and maintain Research
Software.

Commercial forges like GitHub or BitBucket are commonly used by researchers.
Additionally, universities and research institutions may provide institutional
forges, offering the necessary infrastructure for research activities.

Depositing software in a code repository is a form of software distribution
which may happen at a very early stage in the software development.

**The trigger:** Creating software as part of the  research process.

**End result:** Software available on a public repository.

## Action

The author or research team uploads their code to a code repository under a
mutually agreed-upon licence using one of the following version control
systems:

* Git
* Mercurial
* Subversion
* CVS
* Bazaar

Each system comes with its own documentation and unique methods of interaction.

### Prepare code

Following the Software Heritage guidelines:
[https://www.softwareheritage.org/howto-archive-and-reference-your-code/](https://www.softwareheritage.org/howto-archive-and-reference-your-code/),

The repository MUST contain:

* `README` file
* `AUTHORS` file
* licence information in one of the two recommended ways
    * a `LICENCE` file at the root of your project, or
    * a `LICENCES` directory containing all the licences used in your project,
      and an [SPDX compliant](https://spdx.org/licenses/) copyright header in
      all your source code files (see [the REUSE instructions for details and
      tools](https://reuse.software/tutorial/))

The repository SHOULD contain:

* a `codemeta.json` file containing machine-readable metadata (can be produced
  using the [CodeMeta
  Generator](https://codemeta.github.io/codemeta-generator/))

The Research Software MetaData guidelines (a.k.a. [RSMD
guidelines](https://github.com/FAIR-IMPACT/RSMD-guidelines)) offers
recommendations regarding metadata, making Research Software FAIRer.

```json
{
   "@context": "https://w3id.org/codemeta/3.0",
   "type": "SoftwareSourceCode",
   "applicationCategory": "Parallel computing",
   "author": [
       {
           "id": "https://orcid.org/ 0000-0002-7493-5349",
           "type": "Person",
           "affiliation": {
               "type": "Organization",
               "name": "Inria and University Paris Diderot"
           },
           "email": "roberto@dicosmo.org",
           "familyName": "Di Cosmo",
           "givenName": "Roberto"
       },
       {
           "id": "https://orcid.org/ 0000-0002-7433-376X",
           "type": "Person",
           "affiliation": {
               "type": "Organization",
               "name": "University of Pisa"
           },
           "email": "marco.danelutto@unipi.it",
           "familyName": "Danelutto",
           "givenName": "Marco"
       }
   ],
   "codeRepository": "git+https://github.com/rdicosmo/parmap.git",
   "dateModified": "2022-01-03",
   "datePublished": "2011-07-18",
   "license": "https://spdx.org/licenses/LGPL-2.0-only",
   "name": "Parmap",
   "operatingSystem": [
       "Linux",
       "MacOS"
   ],
   "programmingLanguage": "OCaml",
   "relatedLink": "https://opam.ocaml.org/packages/parmap/",
   "version": "1.2.5",
   "developmentStatus": "active",
   "issueTracker": "https://github.com/rdicosmo/parmap/issues",
   "referencePublication": "https://doi.org/10.1016/j.procs.2012.04.202"
}
```

*An example of a codemeta.json file*
