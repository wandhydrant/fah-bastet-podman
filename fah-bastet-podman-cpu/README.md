# Folding@home v8 (Bastet) CPU container for rootless Podman

See [the top-level](..) for common instructions.

## Build image

    podman build -t foldingathome-bastet-cpu-image .

## Run a container

    podman run -d \
      --name fah-bastet-cpu \
      -p 127.0.0.1:7396:7396/tcp \
      -v $YOUR_FAH_DATA_DIRECTORY:/fah \
      foldingathome-bastet-cpu-image
