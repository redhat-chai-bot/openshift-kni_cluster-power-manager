# File-based catalog

The FBC pipeline generates `cluster-power-manager/catalog.json` during the build.
The generated catalog is ignored by Git and must not be committed.

The pipeline:

1. Runs `telco5g-konflux/scripts/catalog/konflux-update-catalog-template.sh` to
   replace the bundle placeholder in `catalog-template.in.yaml` with the image
   from `bundle.builds.in.yaml`.
2. Renders the template with `opm alpha render-template basic` and
   `--migrate-level=bundle-object-to-csv-metadata`.
3. Maps the bundle pullspec to its production repository using `catalog-idms.yaml`.
4. Builds `.konflux/Dockerfile.catalog`, which copies the generated catalog into
   the image and creates its cache.

When updating the release version, keep the channel in `catalog-template.in.yaml`
and the channel label in `.konflux/Dockerfile.bundle` aligned with the major/minor
version in `.konflux/overlay/release.in.yaml`. The bundle metadata channel is
derived from that version during the build.
