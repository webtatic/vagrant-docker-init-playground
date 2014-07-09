# Vagrant Docker init system images

Docker was build making application containers first class citizens, however
that is a huge jump in change, and not one that will easily be established.

Linux containers are more powerful as sub-system containers, able to use the
OS's built in init systems, like systemd, upstart and SysVinit, which existing
tested service scripts are available. These in addition, allow multiple services
to be started at boot without hackery.

Here are several Dockerfiles allowing different linux system containers, each
able to run their OS's built in init system, set up to work as Vagrant docker
images, able to be provisioned using any Vagrant provisioner.

These can run on Host OSs and Cloud infrastructure running latest kernels
(minimum kernel to be determined).

On Host OSs that don't support docker, a Fedora 20 virtualbox will be provisioned
to host the docker containers.

## centos-6
This uses CentOS 6's SysVinit system.

Currently it has a bug in, where agetty on the host will consume high CPU, so
not recommended for use until fixed

    $ vagrant up centos6 --provider=docker

## centos-7
This uses CentOS 7's systemd system.

It is based on the work rhatdan did to get systemd working in docker:
http://rhatdan.wordpress.com/2014/04/30/running-systemd-within-a-docker-container/

    $ vagrant up centos7 --provider=docker

## fedora-20
This uses Fedora 20's systemd system.

It is also based on rhatdan's work.

    $ vagrant up fedora20 --provider=docker


## Testing

I've only tested these on Fedora 20 VirtualBox and AWS hosts, so there may be
issues I've not yet encountered on other host OS's

## Known issues

### CentOS 6 causes the Fedora host's agetty process to run high CPU

Due to running in privileged mode, CentOS 6 for some reason messes with the host's
agetty process.

agetty can be killed, which will stop the CPU issue for that boot

### No finishing "Waiting for machine to boot. This may take a few minutes.."

Sometimes I've found vagrant docker not detect the container as started. I'll
look further at this issue, but at the moment the workaround is to press ctl+c.

The container can then still be ssh'd into, vagrant provision run, etc.