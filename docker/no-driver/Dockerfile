FROM ubuntu:18.04 as ubuntu

LABEL maintainer="cncf-falco-dev@lists.cncf.io"

ARG FALCO_VERSION
ARG VERSION_BUCKET=bin

ENV FALCO_VERSION=${FALCO_VERSION}
ENV VERSION_BUCKET=${VERSION_BUCKET}

WORKDIR /

ADD https://bintray.com/api/ui/download/falcosecurity/${VERSION_BUCKET}/x86_64/falco-${FALCO_VERSION}-x86_64.tar.gz /

RUN apt-get update -y && \
    apt-get install -y libyaml-0-2 binutils && \
    tar -xvf falco-${FALCO_VERSION}-x86_64.tar.gz && \
    rm -f falco-${FALCO_VERSION}-x86_64.tar.gz && \
    mv falco-${FALCO_VERSION}-x86_64 falco && \
    strip falco/usr/bin/falco && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

FROM scratch

COPY --from=ubuntu /lib/x86_64-linux-gnu/libanl.so.1 \
    /lib/x86_64-linux-gnu/libc.so.6 \
    /lib/x86_64-linux-gnu/libdl.so.2 \
    /lib/x86_64-linux-gnu/libgcc_s.so.1 \
    /lib/x86_64-linux-gnu/libm.so.6 \
    /lib/x86_64-linux-gnu/libnsl.so.1 \
    /lib/x86_64-linux-gnu/libnss_compat.so.2 \
    /lib/x86_64-linux-gnu/libnss_files.so.2 \
    /lib/x86_64-linux-gnu/libnss_nis.so.2 \
    /lib/x86_64-linux-gnu/libpthread.so.0 \
    /lib/x86_64-linux-gnu/librt.so.1 \
    /lib/x86_64-linux-gnu/libz.so.1 \
    /lib/x86_64-linux-gnu/

COPY --from=ubuntu /usr/lib/x86_64-linux-gnu/libstdc++.so.6 \
    /usr/lib/x86_64-linux-gnu/libstdc++.so.6

COPY --from=ubuntu /usr/lib/x86_64-linux-gnu/libyaml-0.so.2.0.5 \
    /usr/lib/x86_64-linux-gnu/libyaml-0.so.2

COPY --from=ubuntu /etc/ld.so.cache \
    /etc/nsswitch.conf \
    /etc/ld.so.cache \
    /etc/passwd \
    /etc/group \
    /etc/

COPY --from=ubuntu /etc/default/nss /etc/default/nss
COPY --from=ubuntu /lib64/ld-linux-x86-64.so.2 /lib64/ld-linux-x86-64.so.2

COPY --from=ubuntu /falco /

RUN sed -e 's/time_format_iso_8601: false/time_format_iso_8601: true/' < /etc/falco/falco.yaml > /etc/falco/falco.yaml.new \
    && mv /etc/falco/falco.yaml.new /etc/falco/falco.yaml

CMD ["/usr/bin/falco", "-o", "time_format_iso_8601=true"]