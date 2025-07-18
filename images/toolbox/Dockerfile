# Copyright 2023 Antrea Authors
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

FROM ubuntu:24.04@sha256:440dcf6a5640b2ae5c77724e68787a906afb8ddee98bf86db94eea8528c2c076

LABEL maintainer="Antrea <projectantrea-dev@googlegroups.com>"
LABEL description="A Docker image with useful tools (for networking) based on Ubuntu"

# Keep it in lexicographic order
ENV TOOLS apache2-utils \
          arping \
          conntrack \
          curl \
          dnsutils \
          ethtool \
          iperf3 \
          iproute2 \
          ipset \
          iptables \
          iputils-ping \
          kmod \
          ncat \
          netperf \
          nmap \
          socat \
          tcpdump \
          telnet \
          traceroute \
          wget

# See https://github.com/kubernetes-sigs/iptables-wrappers
ADD --chmod=700 https://raw.githubusercontent.com/kubernetes-sigs/iptables-wrappers/e139a115350974aac8a82ec4b815d2845f86997e/iptables-wrapper-installer.sh /iptables-wrapper-installer.sh

# We run /iptables-wrapper-installer.sh with --no-sanity-check to avoid an issue
# when building the arm64 version of this docker image with qemu:
# Failed to initialize nft: Protocol not supported
RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends $TOOLS && \
    rm -rf /var/cache/apt/* /var/lib/apt/lists/* && \
    /iptables-wrapper-installer.sh --no-sanity-check

COPY VERSION /VERSION

# Use K8s pause binary as the default command.
# This is convenient when using this Docker image to run a K8s Pod which is meant to be exec'ed
# into, as there is no need to specify an explicit command when deploying the Pod.
# "pause" is superior to a simple "sleep infinity", as it handled signals correctly.
# Note that pause is a static binary.
COPY --from=registry.k8s.io/pause:3.10 /pause /pause

CMD ["/pause"]
