OpenBTS on Ubuntu Xenial docker image
=====================================

Building the image
------------------

With recent docker (likely >= 17.x):

    $ docker build --network=host -t openbts-xenial .

With old docker (likely <= 1.x):

    $ docker build -t openbts-xenial .

Running the container
---------------------

To run the container:

    $ docker run -dti --privileged --net=host --name=openbts openbts-xenial

By default, running the container automatically starts openbts inside
the container.

To open a shell in the running container:

    $ docker exec -it openbts /bin/bash

Once connected to the container, openbts can be stopped / restarted
with the following commands:

    $ /home/cxlbadm/openbts_systemd_scripts/openbts-start.sh
    $ /home/cxlbadm/openbts_systemd_scripts/openbts-stop.sh

The openbts cli can be started from inside the container with:

    $ /OpenBTS/OpenBTSCLI

Then, openbts commands can be issued, for example:

    > config Control.LUR.OpenRegistration .*

Running the container in a CorteXlab task
-----------------------------------------

Create the task with the following command for the nodes where you
want to run openbts:

    $ docker run --rm --privileged --net=host --name=openbts openbts-xenial

Additionnal notes
-----------------

- Systemd is used to control the lifecycle of the four daemons needed
  to run openbts: asterisk, sipauthserve, smqueue, openbts. Thus, logs
  for a service can be inspected with:

        $ journalctl -u <service name>

- Running the container in `--privileged` mode is necessary to be able
  to start systemd in the container.

- Some sources say that we need to mount /sys/fs/cgroup (read-only)
  inside the container to be able to run systemd, but it doesn't seem
  necessary. There would be an additionnal option to docker run: `-v
  /sys/fs/cgroup:/sys/fs/cgroup:ro`.

- It is not needed to map specific ports, such as port 49300 (openbts
  cli) to be able to see services from the container host, since the
  container is running in `--net=host` mode.

- Automatic start of openbts when the container runs can be disabled
  by commenting the following line at the end of the Dockerfile:

        RUN systemctl enable openbts

- An OpenSSH server is running inside the container on port 39775,
  allowing to connect to the running container. For example, when
  running the container inside CorteXlab, it allows connecting, as
  cxlbadm, from airlock to the container on a node with a command such
  as:

        $ ssh -p 39775 cxlbadm@mnode<node_number>

  It is then possible to sudo (without password) to root if needed.
