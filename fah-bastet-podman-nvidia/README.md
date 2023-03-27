# Folding@home v8 (Bastet) GPU/Nvidia container for rootless Podman

See [the top-level](..) for common instructions.

You may need to change the base image to be compatible with your
hardware (and Nvidia driver) as well as with the Folding@home Cores.

I assume you have a working installation of Podman
and the [nvidia-driver-toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html#id9).

## Build image

    podman build -t foldingathome-bastet-nvidia-image .

## Run a container

    podman run -d \
      --name fah-bastet-nvidia \
      --security-opt=label=disable \
      -p 127.0.0.1:7396:7396/tcp \
      -v $YOUR_FAH_DATA_DIRECTORY:/fah \
      foldingathome-bastet-nvidia-image

If it's not configured inside your ``/usr/share/containers/containers.conf``,
you might need to add the parameter ``--hooks-dir=/usr/share/containers/oci/hooks.d/`` to the line above.

In the logs you should see, after a little moment, your GPU being mentioned,
and hopefully as "supported". Then you should also be able to "enable" it
in the web app settings.
