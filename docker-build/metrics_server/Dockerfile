# Copyright 2019 Nokia
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

FROM alpine:3.9
MAINTAINER Szekeres Balazs <balazs.szekeres@nokia.com>

ARG DEP_MAN_VERSION
ARG METRICS_SERVER_VERSION
ARG go_install_dir="/usr/local/go"
ARG go_version
ENV GOPATH /build
ENV PATH $go_install_dir/bin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin

ENV VERSION_LDFLAGS='-extldflags "-static"'

RUN apk add --no-cache --virtual .build-deps build-base go godep curl tar git bash mercurial bash \
&&  mkdir -p $go_install_dir \
&&  curl -fsSL -k https://dl.google.com/go/go${go_version}.src.tar.gz | tar zx --strip-components=1 -C ${go_install_dir} \
&&  cd ${go_install_dir}/src/ \
&&  ./make.bash \
&&  if [ $(uname -m) == 'aarch64' ]; then HOST_ARCH=arm64; else HOST_ARCH=amd64; fi \
&&  curl -fsSL -o /usr/local/bin/dep https://github.com/golang/dep/releases/download/v${DEP_MAN_VERSION}/dep-linux-${HOST_ARCH} \
&&  chmod +x /usr/local/bin/dep \
&&  mkdir -p ${GOPATH}/src/github.com/kubernetes-incubator/metrics-server/ \
&&  curl -fsSL -k https://github.com/kubernetes-incubator/metrics-server/archive/v${METRICS_SERVER_VERSION}.tar.gz | tar zx --strip-components=1 -C ${GOPATH}/src/github.com/kubernetes-incubator/metrics-server/ \
&&  cd ${GOPATH}/src/github.com/kubernetes-incubator/metrics-server/ \
&&  dep ensure --vendor-only \
# static linking added
&&  sed -i "s?LDFLAGS=-w \$(VERSION_LDFLAGS)?LDFLAGS='-extldflags "-static"' -w \$(VERSION_LDFLAGS)?" ./Makefile \
&&  make ARCH=${HOST_ARCH} \
&&  mv ./_output/${HOST_ARCH}/metrics-server /metrics-server \
&&  apk del .build-deps \
&&  rm -rf ${GOPATH} \
&&  rm -rf $go_install_dir \
&&  rm -rf /build

