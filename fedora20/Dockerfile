FROM fedora:20
MAINTAINER "Andy Thompson" <andy@webtatic.com>
ENV container docker

# Update all base packages to keep them fresh
RUN yum -y update; yum clean all

# Turn off most systemd services
RUN (cd /lib/systemd/system/sysinit.target.wants/; for i in *; do [ $i == systemd-tmpfiles-setup.service ] || rm -f $i; done); \
rm -f /etc/systemd/system/*.wants/*;\
(cd /lib/systemd/system/multi-user.target.wants/; for i in *; do [ $i == systemd-user-sessions.service ] || rm -f $i; done); \
rm -f /lib/systemd/system/local-fs.target.wants/*; \
rm -f /lib/systemd/system/sockets.target.wants/*udev*; \
rm -f /lib/systemd/system/sockets.target.wants/*initctl*; \
rm -f /lib/systemd/system/basic.target.wants/*;\
rm -f /lib/systemd/system/anaconda.target.wants/*;

# Set up and enable sshd server
RUN yum -y install openssh-server; yum clean all; \
systemctl disable sshd.service; \
systemctl enable sshd.socket; \
echo "UseDNS no" >> /etc/ssh/sshd_config; \
echo "GSSAPIAuthentication no" >> /etc/ssh/sshd_config

# Set up tools used by standard in vagrant
RUN yum -y install wget nfs-utils which; yum clean all

# Set up the vagrant user
RUN useradd vagrant; \
echo vagrant | passwd --stdin vagrant; \
mkdir /home/vagrant/.ssh; \
wget --no-check-certificate \
    'https://raw.githubusercontent.com/mitchellh/vagrant/master/keys/vagrant.pub' \
    -O /home/vagrant/.ssh/authorized_keys; \
chown -R vagrant /home/vagrant/.ssh; \
chmod -R go-rwsx /home/vagrant/.ssh; \
echo "%vagrant ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers.d/vagrant; \
sed -i "s/^.*requiretty/#Defaults requiretty/" /etc/sudoers

# Mount cgroups by default
VOLUME [ "/sys/fs/cgroup" ]

# Set the init command to run at boot by default
CMD ["/usr/sbin/init"]