FROM docker.io/nvidia/cuda:11.8.0-base-ubuntu22.04
LABEL description="Folding@home v8 GPU/Nvidia Container"

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
      ocl-icd-opencl-dev \
      clinfo \
      curl \
    # point at lib mapped in by container runtime
    && mkdir -p /etc/OpenCL/vendors \
    && echo "libnvidia-opencl.so.1" > /etc/OpenCL/vendors/nvidia.icd \
    # next line gets past the fahclient.postinst 
    && touch /usr/bin/systemctl && chmod +x /usr/bin/systemctl \
    # download and verify checksum
    && curl -fsSL \
      https://download.foldingathome.org/releases/beta/fah-client/debian-stable-64bit/release/fah-client_8.1.18_amd64.deb \
      -o fah.deb \
    && echo "27807eb85dc505342ece480b7bfe7bf4851da8eee4542410f66f758eb42fceef fah.deb" \
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
