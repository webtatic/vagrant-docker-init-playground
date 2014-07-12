FROM centos:centos6
MAINTAINER "Andy Thompson" <andy@webtatic.com>
ENV container docker

# Update all base packages to keep them fresh
RUN yum -y update; yum clean all

# Install initscripts, but turn off all services by default
RUN yum -y install initscripts; yum clean all; rm /etc/rc.d/rc*.d/*

# Disable ttys
RUN mv /etc/init/serial.conf /etc/init/serial.conf.disabled; \
mv /etc/init/tty.conf /etc/init/tty.conf.disabled; \
mv /etc/init/start-ttys.conf /etc/init/start-ttys.conf.disabled

# Set up and enable sshd server
RUN yum -y install openssh-server; yum clean all; \
chkconfig sshd on; \
echo "UseDNS no" >> /etc/ssh/sshd_config; \
echo "GSSAPIAuthentication no" >> /etc/ssh/sshd_config

# Set up tools used by standard in vagrant
RUN yum -y downgrade libselinux libselinux-utils; yum -y install wget nfs-utils which sudo; yum clean all

# Set up the vagrant user
RUN echo "NETWORKING=yes" > /etc/sysconfig/network; \
useradd vagrant; \
echo vagrant:vagrant | chpasswd; \
mkdir /home/vagrant/.ssh; \
wget --no-check-certificate \
    'https://raw.githubusercontent.com/mitchellh/vagrant/master/keys/vagrant.pub' \
    -O /home/vagrant/.ssh/authorized_keys; \
chown -R vagrant /home/vagrant/.ssh; \
chmod -R go-rwsx /home/vagrant/.ssh; \
echo "%vagrant ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers.d/vagrant; \
sed -i "s/^.*requiretty/#Defaults requiretty/" /etc/sudoers

# Set the init command to run at boot by default
CMD ["/sbin/init"]