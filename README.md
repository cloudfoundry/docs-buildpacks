# docs-buildpacks

This is a guide for developers about using buildpacks with Cloud Foundry.
Buildpacks package the languages and libraries that support your apps.

The contents here are structured as a topic repository intended to be
compiled into a larger document with [Bookbinder](http://github.com/cloudfoundry-incubator/bookbinder).

See the [docs-book-cloudfoundry](http://github.com/cloudfoundry/docs-book-cloudfoundry)
repository for the complete list of open source documentation repositories, as well as information about the publishing process.

`docs-buildpacks` uses only the `master` branch to publish the buildpack documentation. The buildpack documentation is not tied to specific versions of Cloud Foundry. If you need to add version-specific content, add a template variable in the book repo for that version.

### Style Sheet

Use this section to specify spelling of special words for buildpacks:

+ Do not use variables to represent "product-names" in these files.
  The buildpacks are not proprietary but belong to Cloud Foundry.

## Branch map

| Branch  | EART version     | Doc Link      |
|---------|------------------|---------------|
| 10.5    | EART 10.5        | [EART v10.5 staging](https://author-techdocs2-prod.adobecqms.net/us/en/vmware-tanzu/platform/elastic-application-runtime/10-5/eart/runtime-rn.html)
| tcf-104 | EART 10.4        | [EART v10.4](https://techdocs.broadcom.com/us/en/vmware-tanzu/platform/elastic-application-runtime/10-4/eart/runtime-rn.html)
| tcf-103 | EART 10.3        | [EART v10.3](https://techdocs.broadcom.com/us/en/vmware-tanzu/platform/elastic-application-runtime/10-3/eart/concepts-overview.html) |
| tcf-102 | TPCF 10.2        | [TPCF v10.2](https://techdocs.broadcom.com/us/en/vmware-tanzu/platform/elastic-application-runtime/10-2/eart/concepts-overview.html) |
| tcf-10  | TPCF 10.0        | archived PDF |
| 6.0     | TAS 6.0          | [TAS v6.0](https://techdocs.broadcom.com/us/en/vmware-tanzu/platform/elastic-application-runtime/6-0/eart/concepts-overview.html) |
| 5.0     | TAS 5.0 (EOGS)   | archived PDF  |
| 4.0     | TAS v4.0 (EOGS)  | archived PDF  |
| 3.0     | TAS v3.0 (EOGS)  | archived PDF  |
| 2.13    | TAS v2.13 (EOGS) | archived PDF  |
| 2.12    | TAS v2.12 (EOGS) | archived PDF  |
| 2.11    | TAS v2.11 (EOGS) | archived PDF  |