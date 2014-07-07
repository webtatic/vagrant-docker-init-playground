FROM centos:centos6
MAINTAINER "Andy Thompson" <andy@webtatic.com>
ENV container docker

RUN yum -y update; yum clean all

RUN yum -y install initscripts; yum clean all; rm /etc/rc.d/rc*.d/*

RUN yum -y install openssh-server; yum clean all; chkconfig sshd on

RUN echo "UseDNS no" >> /etc/ssh/sshd_config; \
echo "GSSAPIAuthentication no" >> /etc/ssh/sshd_config

RUN yum -y downgrade libselinux libselinux-utils; yum -y install wget nfs-utils which sudo; yum clean all

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

CMD ["/sbin/init"]