FROM centos:7
MAINTAINER inforedaster@gmail.com

ENV container docker

ARG ORACLE_JDK_VERSION
ARG ORACLE_JDK_BUILD_NUMBER

# Install Oracle JDK
RUN curl -vfL -H "Cookie: oraclelicense=accept-securebackup-cookie" -o /tmp/jdk-8u121-linux-x64.rpm http://download.oracle.com/otn-pub/java/jdk/8u121-b13/e9e7ea248e2c4826b92b3f075a80e441/jdk-8u121-linux-x64.rpm && \
rpm -ivh /tmp/jdk-8u121-linux-x64.rpm && \
rm -f /tmp/*;

# install tools
RUN yum clean all && \
    yum -y install epel-release openssh-server curl && \
    yum -y install maven git unzip lsof python-pip software-properties-common ca-certificates apt-utils wget lxc;

# Install Docker from Docker Inc. repositories.
RUN curl -sSL https://get.docker.com/ | sh

# update sshd settings, create jenkins user, set jenkins user pw, generate ssh keys
RUN sed -i 's|session    required     pam_loginuid.so|session    optional     pam_loginuid.so|g' /etc/pam.d/sshd \
    && mkdir -p /var/run/sshd \
    && useradd -u 1000 -m -s /bin/bash jenkins \
    && echo "jenkins:jenkins" | chpasswd \
    && usermod -aG wheel jenkins \
    #&& echo 'jenkins ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers \
    #&& echo "jenkins    ALL=(ALL)       ALL" >> /etc/sudoers.d/jenkins \
    && usermod -a -G docker jenkins \
    && /usr/bin/ssh-keygen -A \
    && echo export JAVA_HOME="/`alternatives  --display java | grep best | cut -d "/" -f 2-6`" >> /etc/environment

# Set java environment
ENV JAVA_HOME /etc/alternatives/jre

# Install the magic wrapper.
ADD wrapdocker /usr/local/bin/wrapdocker
RUN chmod +x /usr/local/bin/wrapdocker
VOLUME /var/lib/docker

WORKDIR /opt/jenkins
EXPOSE 22

# place the jenkins slave startup script into the container
ADD jenkins-slave-startup.sh /
RUN chmod +x /jenkins-slave-startup.sh
CMD ["/jenkins-slave-startup.sh"]

