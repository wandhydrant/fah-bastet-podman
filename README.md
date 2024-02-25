# Folding@home v8 (Bastet) containers for rootless Podman

Based on the [official GPU containers for v7](https://github.com/FoldingAtHome/containers)
but for usage with the [new v8 client](https://github.com/FoldingAtHome/fah-client-bastet)
(currently in beta) and rootless Podman (instead of Docker).

Tested with Podman 4.3.1 on a Debian 12.5 (bookworm) host.

*These are inofficial containers that fit my personal purposes.*
There is [one CPU version](fah-bastet-podman-cpu) and
[one GPU/Nvidia version](fah-bastet-podman-nvidia). 
Go there for build and run instructions; below are the common steps.
You may need to adapt base images if my choices do not
fit your hardware setup; you may also need to change the client version
(and the checksum) if there is a newer one.

## Why?

It is certainly easier to run the fah-client directly on your system.
But for better isolation, policy reasons, or re-use of existing
infrastructures, you may prefer to run it in a container.

I like podman as it does not require a daemon, and containers can be run
from a non-root account (by now, Docker can do that, too).

## Prepare configuration

Create a ``fah-data`` directory which will be mounted in the container;
then write your ``fah-data/config.xml``, for example:

    <config>
      <user v='YOUR_USERNAME'/>
      <passkey v='YOUR_PASSKEY'/>
      <cpus v='<config>
      <verbosity v='5'/>
      <log-level v='true'/>
      <http-addresses v='0.0.0.0:7396'/>
    </config>

As for the ``http-addresses`` line - requests from the host will appear inside
the container as coming from an (internal) IP address, not localhost.
Inside the container, there is only one "network interface", for communicating
with the host. So inside the container, fah-client must listen to
"all interfaces". With the default ``127.0.0.1:7396`` it could not
serve any requests.

## Ownership of your ``fah-data`` directory

When using rootless podman, your container user
will be mapped to a sub-uid associated with your system user.
This is why I don't re-use the ``--user "$(id -u):$(id -g)"``
[line of the v7 Dockerfile](https://github.com/FoldingAtHome/containers/tree/master/fah-gpu#start-folding-on-a-single-machine); instead we have the container user
"fah-client" and set the permissions of the ``fah-data`` directory accordingly.
You could calculate these permissions, or just cheat and have your
``fah-data`` directory for a short moment world-writeable, see what user
and group ID the created files have, and then set the correct
permissions with ``sudo chown -R UID:GID FAH_DATA_DIR``.

## Using the web app to start and control folding

The v7 client would have CLI options such as ``--send-finish`` or
``send-unpause``; for v8, it seems that you need to use the web app.
Also, just to start getting CPU units or to enable the GPU,
I had to go once into the web app. Once running, the web app need not
be open for new work units to arrive.

Connect to [https://beta.foldingathome.org](https://beta.foldingathome.org) (or a
[local version](https://github.com/FoldingAtHome/fah-web-client-bastet))
from a browser on your podman host, and you should be able to control
your client.
  
## Connecting to a headless fah-client box

If your fah-client container is running on a headless box, one possibility
is to make a SSH redirection from your desktop machine (where the browser
is running), like this:

    ssh -fNL 127.0.0.1:7396:127.0.0.1:7396 myuser@fahclient-container-host
