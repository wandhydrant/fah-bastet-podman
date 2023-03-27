# Folding@home v8 CPU Container

FROM debian:bullseye-slim
LABEL description="Folding@home v8 CPU Container"

RUN apt-get update \
    && apt-get install -y --no-install-recommends ca-certificates curl \
    # next line gets past the fahclient.postinst
    && touch /usr/bin/systemctl && chmod +x /usr/bin/systemctl \
    # download and verify checksum
    && curl -fsSL \
      https://download.foldingathome.org/releases/beta/fah-client/debian-stable-64bit/release/fah-client_8.1.16_amd64.deb \
      -o fah.deb \
    && echo "0f342d0d09d521346b2b7901a9f15c120b73c0237523d294cf02efc6ae201bb6 fah.deb" \
      | sha256sum -c --strict - \
    # install and cleanup; dpkg will complain about missing systemctl but that is OK
    && DEBIAN_FRONTEND=noninteractive dpkg --install fah.deb && rm fah.deb \
    && apt-get purge --autoremove -y curl \
    && rm -rf /var/lib/apt/lists/* /var/cache/apt/archives/*

WORKDIR "/fah"
VOLUME ["/fah"]

USER "fah-client"

EXPOSE 7396/tcp

ENTRYPOINT ["/usr/bin/fah-client"]
