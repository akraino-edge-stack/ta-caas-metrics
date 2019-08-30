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
ENV PATH /usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin

ENV VERSION_LDFLAGS='-extldflags "-static"'

RUN apk add --no-cache --virtual .build-deps build-base go godep curl tar git bash mercurial bash \
&&  mkdir -p $go_install_dir \
&&  curl -fsSL -k https://dl.google.com/go/go${go_version}.src.tar.gz | tar zx --strip-components=1 -C ${go_install_dir} \
&&  cd ${go_install_dir}/src/ \
&&  ./make.bash \
&&  curl -fsSL -o /usr/local/bin/dep https://github.com/golang/dep/releases/download/v${DEPENDENCY_VERSION}/dep-linux-amd64 \
&&  chmod +x /usr/local/bin/dep \
&&  mkdir -p ${GOPATH}/src/github.com/kubernetes-incubator/metrics-server/ \
&&  curl -fsSL -k https://github.com/kubernetes-incubator/metrics-server/archive/v${METRICS_SERVER_VERSION}.tar.gz | tar zx --strip-components=1 -C ${GOPATH}/src/github.com/kubernetes-incubator/metrics-server/ \
&&  cd ${GOPATH}/src/github.com/kubernetes-incubator/metrics-server/ \
&&  dep ensure --vendor-only \
# static linking added
&&  sed -i "s?LDFLAGS=-w \$(VERSION_LDFLAGS)?LDFLAGS='-extldflags "-static"' -w \$(VERSION_LDFLAGS)?" ./Makefile \
&&  make \
&&  mv ./_output/amd64/metrics-server /metrics-server \
&&  apk del .build-deps \
&&  rm -rf ${GOPATH} \
&&  rm -rf /build

