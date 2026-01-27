# Plan: Merging "Creating custom buildpacks" and "Customizing and developing buildpacks"

## Overview
This plan outlines how to merge two related documentation topics:
- **`custom.html.md.erb`** - "Creating custom buildpacks for Cloud Foundry" (234 lines)
- **`developing-buildpacks.html.md.erb`** - "Customizing and developing buildpacks in Cloud Foundry" (42 lines)

Both topics address similar content but serve different purposes:
- `developing-buildpacks.html.md.erb` acts as an overview/index page with links to related topics
- `custom.html.md.erb` contains detailed technical content about creating and using custom buildpacks

## Content Analysis

### `developing-buildpacks.html.md.erb` contains:
1. **Introduction** (lines 6-8): Overview of buildpacks and Cloud Foundry's interface
2. **Customizing and creating buildpacks** section (lines 10-23):
   - Options: Community buildpacks, Heroku buildpacks, custom buildpacks
   - Links to `custom.html` and `depend-pkg-offline.html`
3. **Maintaining buildpacks** section (lines 25-35):
   - Links to `merging_upstream.html.md.erb` and `upgrading_dependency_versions.html.md.erb`
   - Link to `prod-server.html`
4. **Using CI for buildpacks** section (lines 37-41):
   - Link to `buildpack-ci-index.html`

### `custom.html.md.erb` contains:
1. **Introduction** (lines 6-7): Brief statement about creating custom buildpacks
2. **Important notes** (lines 9-16): VMware/Tanzu notices
3. **Packaging custom buildpacks** (lines 19-127): Detailed technical content
4. **Core buildpack communication contract** (lines 115-166): Technical specifications
5. **Deploying apps with a custom buildpack** (lines 168-234): Usage instructions

## Proposed Merge Strategy

### Option A: Merge into `custom.html.md.erb` (Recommended)
**Rationale**: `custom.html.md.erb` is the more comprehensive technical document and is already referenced by `developing-buildpacks.html.md.erb`.

**New structure for merged `custom.html.md.erb`**:
1. **Title**: Keep "Creating custom buildpacks for Cloud Foundry" (or consider "Customizing and developing buildpacks in Cloud Foundry")
2. **Introduction** (merge both intros):
   - Start with overview from `developing-buildpacks.html.md.erb` (lines 6-8)
   - Follow with existing intro from `custom.html.md.erb` (lines 6-7)
   - Keep important notes (lines 9-16)
3. **Customizing and creating buildpacks** (new section, early in doc):
   - Add the options section from `developing-buildpacks.html.md.erb` (lines 12-19)
   - Remove the self-reference link to `custom.html` (since we're in that file)
   - Keep link to `depend-pkg-offline.html`
4. **Packaging custom buildpacks** (existing section, keep as-is)
5. **Core buildpack communication contract** (existing section, keep as-is)
6. **Deploying apps with a custom buildpack** (existing section, keep as-is)
7. **Maintaining buildpacks** (new section, from `developing-buildpacks.html.md.erb`):
   - Add after "Deploying apps" section
   - Keep all links to related topics
8. **Using CI for buildpacks** (new section, from `developing-buildpacks.html.md.erb`):
   - Add as final section

### Option B: Merge into `developing-buildpacks.html.md.erb`
**Rationale**: The title "Customizing and developing buildpacks" is more comprehensive.

**Considerations**: Would require moving all technical content from `custom.html.md.erb`, which is more disruptive.

## Files to Update

### 1. `custom.html.md.erb`
- Add merged content from `developing-buildpacks.html.md.erb`
- Reorganize sections as outlined above
- Remove any duplicate content (e.g., the note about forking buildpacks appears in both)

### 2. `classic.html.md.erb`
- **Current**: Links to `developing-buildpacks.html`
- **Update**: Change link to point to `custom.html` (or new merged file)
- **Line 34**: Update reference

### 3. `developing-buildpacks.html.md.erb`
- **Action**: Delete this file after merge is complete

## Content Decisions Needed

### 1. Title
- **Option 1**: Keep "Creating custom buildpacks for Cloud Foundry"
- **Option 2**: Change to "Customizing and developing buildpacks in Cloud Foundry"
- **Recommendation**: Option 2 is more comprehensive and matches the broader scope

### 2. Duplicate Content
- Both files mention "forking existing buildpacks and syncing patches"
- **Location in `custom.html.md.erb`**: End of "Deploying apps" section (lines 232-234)
- **Location in `developing-buildpacks.html.md.erb`**: In "Customizing and creating" section (line 18)
- **Decision**: Keep the more detailed version (in `custom.html.md.erb` with GitHub link) and remove the brief mention

### 3. Section Ordering
- **Proposed order**:
  1. Introduction & overview
  2. Customizing and creating buildpacks (options)
  3. Packaging custom buildpacks
  4. Core buildpack communication contract
  5. Deploying apps with a custom buildpack
  6. Maintaining buildpacks
  7. Using CI for buildpacks
- **Rationale**: Logical flow from "what are my options" → "how to create" → "how to use" → "how to maintain"

### 4. Subnav Links
- `developing-buildpacks.html.md.erb` uses `class="subnav"` for some links
- **Decision**: Keep subnav classes where appropriate, or remove if not needed in merged structure

## Implementation Steps

1. **Backup current files** (already done - changes reversed)
2. **Update `custom.html.md.erb`**:
   - Add introduction from `developing-buildpacks.html.md.erb`
   - Add "Customizing and creating buildpacks" section early
   - Add "Maintaining buildpacks" section after deployment section
   - Add "Using CI for buildpacks" section at end
   - Remove duplicate content
3. **Update `classic.html.md.erb`**:
   - Change reference from `developing-buildpacks.html` to `custom.html`
4. **Verify all links**:
   - Check that all internal links still work
   - Ensure no broken references
5. **Delete `developing-buildpacks.html.md.erb`**
6. **Run linter** to check for errors
7. **Search for other references** to `developing-buildpacks.html` in codebase

## Potential Issues

1. **Anchor IDs**: Both files use anchor IDs (e.g., `id="create-buildpacks"`). Need to ensure no conflicts.
2. **Cross-references**: Other files might link to `developing-buildpacks.html`. Need to find and update all references.
3. **Navigation structure**: If there's a navigation menu, it may need updating.
4. **Search for references**: Use `grep -r "developing-buildpacks"` to find all references.

## Questions for Review

1. **Title preference**: Which title should we use for the merged document?
2. **Section ordering**: Does the proposed order make sense, or should sections be reordered?
3. **Content to keep/remove**: Are there any sections that should be removed or consolidated?
4. **Link structure**: Should we keep the subnav link classes or remove them?
5. **Anchor IDs**: Should we preserve existing anchor IDs or create new ones?

## Next Steps

1. Review and refine this plan
2. Confirm title choice
3. Approve section ordering
4. Execute merge once approved
