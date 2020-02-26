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

+ **_CF-compatible CNB ** The original term proposed was "shimmed buildpack".
    Do not capitalize compatible and do hyphenate CF-compatible.
    No need to add buildpack at the end because the "B" in CNB stands for "buildpack".
    It does not stand for "bundle" as I originally thought).
