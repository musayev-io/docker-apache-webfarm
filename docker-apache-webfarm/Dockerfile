FROM centos:centos6
MAINTAINER musa@msuayev.io

# Install wget
RUN yum -y install wget
# Install EPEL and clean-up
RUN wget http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
RUN rpm -ivh epel-release-6-8.noarch.rpm
RUN rm -rf epel-release-6-8.noarch.rpm
# Update Yum and install services
RUN yum -y update
RUN yum -y install which sudo httpd php openssh-server
# Start HTTP and SSH daemons
RUN echo $' \n\
# Start HTTP and SSH Daemon \n\
/sbin/service httpd start \n\
/sbin/service sshd start' >> ~/.bashrc
