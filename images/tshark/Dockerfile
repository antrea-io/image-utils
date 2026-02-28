# Copyright 2026 Antrea Authors
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

FROM ubuntu:24.04@sha256:d1e2e92c075e5ca139d51a140fff46f84315c0fdce203eab2807c7e495eff4f9

LABEL maintainer="Antrea <projectantrea-dev@googlegroups.com>"
LABEL description="A Docker image with tshark based on Ubuntu"

# We follow the recommended approach for running tshark as a non-root user on Debian-based systems:
# https://gitlab.com/wireshark/wireshark/-/raw/master/packaging/debian/README.Debian
# By pre-seeding the debconf answer and running dpkg-reconfigure, wireshark-common creates the
# wireshark group and grants dumpcap the CAP_NET_RAW and CAP_NET_ADMIN file capabilities via
# setcap (the setuid-root bit is only used as a fallback on kernels without capability support).
# Members of the wireshark group can then capture packets without running as root.
# Note that file capabilities are bounded by the container's capability set: NET_RAW and NET_ADMIN
# must still be granted at runtime via the Pod securityContext for captures to work.
# debconf-utils is purged after use to keep the image size small.
RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends debconf-utils tshark=4.2.2-1.1build3 && \
    echo "wireshark-common wireshark-common/install-setuid boolean true" | debconf-set-selections && \
    DEBIAN_FRONTEND=noninteractive dpkg-reconfigure wireshark-common && \
    DEBIAN_FRONTEND=noninteractive apt-get purge -y debconf-utils && \
    apt-get autoremove --purge -y && \
    rm -rf /var/cache/apt/* /var/lib/apt/lists/* && \
    useradd -m tshark && \
    usermod -aG wireshark tshark

USER tshark

# Use K8s pause binary as the default command.
# This is convenient when using this Docker image to run a K8s Pod which is meant to be exec'ed
# into, as there is no need to specify an explicit command when deploying the Pod.
# "pause" is superior to a simple "sleep infinity", as it handles signals correctly.
# Note that pause is a static binary.
COPY --from=registry.k8s.io/pause:3.10 /pause /pause

CMD ["/pause"]
