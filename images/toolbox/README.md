# antrea/toolbox

This Docker image is based on Ubuntu and includes several networking utilities
useful for troubleshooting and testing container networks.

While we try to keep the size of the image on the smaller side, this is not a
priority. If you believe that an important tool is missing, feel free to suggest
adding it to the image.

The antrea/toolbox image is built and pushed to the registry every time this
directory is updated in the main branch. The image tag will be set to the
version number stored in the `VERSION` file. When updating the software included
in the image, please update the version number and update the table below.

Here is a changelog for the different versions of this image:

| Tag             | Change                                                     |
| :---------------| ---------------------------------------------------------- |
| 1.0             | Initial version.                                           |
| 1.1             | Use pause as the default command.                          |
| 1.2             | Add netperf to image.                                      |
| 1.3             | Add Windows support.                                       |
| 1.4             | Add kmod to image.                                         |
| 1.5             | Update Linux base image to ubuntu:24.04.                   |
| 1.6             | Add ipset to Linux image.                                  |
