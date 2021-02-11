
Welcome, this is lazy-autobuild.
Thu GNU General Public License v3 licesed (mostly) automatic build system for Lazybox.
Lazybox for the uninitiated is a inux distribution designed to not use _GNU_, sadly this is quit hard. GNU make is still available inside this environment.



 # Usage

## Fase 1: Docker
This will create an llvm/musl toolchain.
I've heard that you guys don't like waiting for code to be compiled.
A docker swarm to the recue, completely over engineered.

Make your main pc/laptop the manager of the swarm using:
`docker swarm init --advertise-addr [The IP-address of your device you want to use local/global]`

It will return a command that allows your other pcs to join, use it.

On the main node run: `docker network create -d overlay my_docker_net`

The problem is that we can't chain commands when starting a docker service create, so we'll have to modify the base abyssos/abyss:dev docker container. We'll need to dowload packages for the installation, we'll add these to the our modified package.
This method might also reduce internet usage.

To initialize run ```docker_init_clean```, this will create lazy_abyss.tar, this will have to be manually moved to all docker nodes and then loaded using: ``` docker load -i lazy_abyss.tar```

To start the distributed compiler `docker_start_farm` can be used. Prior to execution modify `docker_start_farm` to suit the number of jobs you need to run.

Run ```docker ps -a``` to find the container id of your work nodes
and execute a shell on a node of your chosing using. This can be done as follows:
`docker exec -it [container id] /bin/sh`

Modify DISTCC_HOSTS in `compile` in one container to point to all the docker distcc volunteers. Afterwards run `compile`.
When your compiles are finished, `docker_rm_farm` can be used to remove the compile farm.
It could be useful to leave the compile farm around for the compilation indide the chroot. This is not yet supported.

## Fase 2: Chroot

This part is significantly less over engineerd (luckily).
There are 4 commands the user should be aware of, they are writen in chronological order.

* chroot_prepare_lazy

  This creates the chroot and cleans the most important locations (/lib, /bin, /sbin, /usr, /lazybox, /etc).
* chroot_fetch

  This command will fetch the sources needed to get curl working inside the chroot. And some files for to be used inside the chroot environmen. This command will automatically be execute by `chroot_prepare_lazy`. It it __NOT NEEDED__ to be manually exected. But it can be.
* chroot_lazy

  This will chroot into the lazybox environment.
* inside_chroot

  This is the script that has to be executed inside the chroot to compile all the required apckages in the correct order.
  This will also generate a

## Fase 3: Boot

Use virt-manager or qemu to boot the `lazybox.img`.

Or execute QEMU directly with the following

```
qemu-system-x86_64 path/to/disk -enable-kvm -m 4096
```

Sources:
*	https://www.youtube.com/watch?v=nGSNULpHHZc
*	https://docs.docker.com/network/overlay/
*	https://docs.docker.com/registry/

