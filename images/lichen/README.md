# antrea/lichen

This Docker image includes the [lichen](https://github.com/uw-labs/lichen) tool,
used to check the licenses of all the dependencies included in a Go binary.

To build a new version of the image and push it to Dockerhub, use the `Update
antrea/lichen Docker image` Github workflow. Only contributors with
`write` access to this Github repository can trigger the workflow. If you need
to update the image, please check with an Antrea maintainer first.
