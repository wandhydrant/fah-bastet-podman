# Folding@home v8 CPU Container

FROM debian:bookworm-slim
LABEL description="Folding@home v8 CPU Container"

RUN apt-get update \
    && apt-get install -y --no-install-recommends ca-certificates curl \
    # next line gets past the fahclient.postinst
    && touch /usr/bin/systemctl && chmod +x /usr/bin/systemctl \
    # group must be present for the FAH install script
    && groupadd render \
    # download and verify checksum
    && curl -fsSL \
      https://download.foldingathome.org/releases/public/fah-client/debian-10-64bit/release/fah-client_8.3.17_amd64.deb \
      -o fah.deb \
    && echo "dc3821da43d7d93ebe686212917fc156ea27d7c8ecc1093039e4193a56de1ab6 fah.deb" \
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
