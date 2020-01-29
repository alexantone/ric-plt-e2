##############################################################################
#
#   Copyright (c) 2019 AT&T Intellectual Property.
#
#   Licensed under the Apache License, Version 2.0 (the "License");
#   you may not use this file except in compliance with the License.
#   You may obtain a copy of the License at
#
#       http://www.apache.org/licenses/LICENSE-2.0
#
#   Unless required by applicable law or agreed to in writing, software
#   distributed under the License is distributed on an "AS IS" BASIS,
#   WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
#   See the License for the specific language governing permissions and
#   limitations under the License.
#
##############################################################################
#
#   This source code is part of the near-RT RIC (RAN Intelligent Controller)
#   platform project (RICP).
#

#
#   This source code is part of the near-RT RIC (RAN Intelligent Controller)
#   platform project (RICP).
#
#ARG DOCKER_REPO=nexus3.o-ran-sc.org:10004
ARG DOCKER_REPO=akrainoenea

FROM ${DOCKER_REPO}/bldr-ubuntu16-c-go:3-u16.04-nng as ubuntu

WORKDIR /opt/e2/

RUN mkdir -p /opt/e2/RIC-E2-TERMINATION/ \
    && mkdir -p /opt/e2/RIC-E2-TERMINATION/TEST/T1 \
    && mkdir -p /opt/e2/RIC-E2-TERMINATION/TEST/T2
COPY . /opt/e2/RIC-E2-TERMINATION/
RUN mv /opt/e2/RIC-E2-TERMINATION/CMakeLists.txt /opt/e2/

# Install RMR library and dev files
ENV RMR_VER=1.10.0
RUN git clone https://gerrit.o-ran-sc.org/r/ric-plt/lib/rmr -b ${RMR_VER} \
    && mkdir -p rmr/build \
    && cd rmr/build \
    && ( cmake -DDEV_PKG=1 .. && make install && ldconfig ) \
    && ( cmake -DDEV_PKG=0 .. && make install && ldconfig )

RUN apt-get install -y autoconf gawk libtool automake pkg-config autoconf-archive \
    && git clone http://gerrit.o-ran-sc.org/r/com/log \
    && cd log \
    && ./autogen.sh && ./configure && make && make install && ldconfig \
    && cd /opt/e2/ && /usr/local/bin/cmake . && make

#    && git clone http://gerrit.o-ran-sc.org/r/ric-plt/tracelibcpp \
#    && cd tracelibcpp && mkdir build && cd build  \
#    &&  sed -i '19iset\(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -O3"\)' ../CMakeLists.txt \
#    &&  sed -i '19iset\(CMAKE_CXX_STANDARD 17\)' ../CMakeLists.txt\
#    && /usr/local/bin/cmake .. && make && cd .. && cp include/tracelibcpp/tracelibcpp.hpp . && cd .. \
#    && mkdir nlohmann && cd nlohmann && find / -type f -name "json.hpp" -exec cp {} json.hpp \; && cd .. \
#    && find / -type d -name "opentracing" -exec cp -r {} . \; \
#    && cd /usr/local/lib/ && find / -type f -name "libyaml-cpp.a" -exec cp {} libyaml-cpp.a \; \
#    && find / -type f -name "libopentracing.a" -exec cp {} libopentracing.a \; && cd /opt/e2/RIC-E2-TERMINATION && ls nlohmann  \

FROM ubuntu:16.04
RUN apt-get update && apt-get install -y net-tools iputils-ping curl tcpdump

#ARG ARCH=x86_64
ARG ARCH=aarch64

COPY --from=ubuntu /opt/e2/e2 /opt/e2/e2
COPY --from=ubuntu /opt/e2/RIC-E2-TERMINATION/dockerRouter.txt /opt/e2/dockerRouter.txt
#COPY --from=ubuntu /opt/e2/RIC-E2-TERMINATION/tracelibcpp/build/libtracelibcpp.so /usr/local/lib/libtracelibcpp.so
#COPY --from=ubuntu /opt/e2/RIC-E2-TERMINATION/tracelibcpp/build/libtracelibcpp.so.0 /usr/local/lib/libtracelibcpp.so.0
#COPY --from=ubuntu /opt/e2/RIC-E2-TERMINATION/tracelibcpp/build/libtracelibcpp.so.0.0.2 /usr/local/lib/libtracelibcpp.so.0.0.2
#COPY --from=ubuntu /usr/local/lib/librmr_nng.a /usr/local/lib/librmr_nng.a
COPY --from=ubuntu /usr/local/lib/librmr_nng.so.1 /usr/local/lib/librmr_nng.so.1
COPY --from=ubuntu /usr/local/lib/librmr_nng.so /usr/local/lib/librmr_nng.so
COPY --from=ubuntu /usr/local/lib/librmr_nng.so.1.10.0 /usr/local/lib/librmr_nng.so.1.10.0
COPY --from=ubuntu /usr/local/lib/libnng.so.1 /usr/local/lib/libnng.so.1
COPY --from=ubuntu /usr/local/lib/libmdclog.so.0 /usr/local/lib/libmdclog.so.0

COPY --from=ubuntu /usr/lib/libboost_log_setup.so /usr/lib/libboost_log_setup.so
COPY --from=ubuntu /usr/lib/libboost_log_setup.so.1.69.0 /usr/lib/libboost_log_setup.so.1.69.0

COPY --from=ubuntu /usr/lib/libboost_log.so /usr/lib/libboost_log.so
COPY --from=ubuntu /usr/lib/libboost_log.so.1.69.0 /usr/lib/libboost_log.so.1.69.0

COPY --from=ubuntu /usr/lib/libboost_thread.so /usr/lib/libboost_thread.so
COPY --from=ubuntu /usr/lib/libboost_thread.so.1.69.0 /usr/lib/libboost_thread.so.1.69.0

COPY --from=ubuntu /usr/lib/libboost_filesystem.so /usr/lib/libboost_filesystem.so
COPY --from=ubuntu /usr/lib/libboost_filesystem.so.1.69.0 /usr/lib/libboost_filesystem.so.1.69.0

COPY --from=ubuntu /usr/lib/libboost_regex.so /usr/lib/libboost_regex.so
COPY --from=ubuntu /usr/lib/libboost_regex.so.1.69.0 /usr/lib/libboost_regex.so.1.69.0

COPY --from=ubuntu /usr/lib/${ARCH}-linux-gnu/libicui18n.so /usr/lib/${ARCH}-linux-gnu/libicui18n.so
COPY --from=ubuntu /usr/lib/${ARCH}-linux-gnu/libicui18n.so.55 /usr/lib/${ARCH}-linux-gnu/libicui18n.so.55
COPY --from=ubuntu /usr/lib/${ARCH}-linux-gnu/libicui18n.so.55.1 /usr/lib/${ARCH}-linux-gnu/libicui18n.so.55.1

COPY --from=ubuntu /usr/lib/${ARCH}-linux-gnu/libicuuc.so /usr/lib/${ARCH}-linux-gnu/libicuuc.so
COPY --from=ubuntu /usr/lib/${ARCH}-linux-gnu/libicuuc.so.55 /usr/lib/${ARCH}-linux-gnu/libicuuc.so.55
COPY --from=ubuntu /usr/lib/${ARCH}-linux-gnu/libicuuc.so.55.1 /usr/lib/${ARCH}-linux-gnu/libicuuc.so.55.1

COPY --from=ubuntu /usr/lib/${ARCH}-linux-gnu/libicudata.so /usr/lib/${ARCH}-linux-gnu/libicudata.so
COPY --from=ubuntu /usr/lib/${ARCH}-linux-gnu/libicudata.so.55 /usr/lib/${ARCH}-linux-gnu/libicudata.so.55
COPY --from=ubuntu /usr/lib/${ARCH}-linux-gnu/libicudata.so.55.1 /usr/lib/${ARCH}-linux-gnu/libicudata.so.55.1


WORKDIR /opt/e2/
ENV LD_LIBRARY_PATH=/usr/local/lib
ENV RMR_SEED_RT=dockerRouter.txt
ENV nano=38000
ENV loglevel=info
ENV volume="."
EXPOSE 38000
CMD ["sh", "-c", "./e2 nano $nano loglevel $loglevel volume $volume"]
