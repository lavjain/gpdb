FROM centos:centos6

## Install some basic utilities and build tools
RUN yum makecache && \
    rpm --import http://mirror.centos.org/centos/RPM-GPG-KEY-CentOS-6 && \
    yum -y install epel-release java-1.7.0-openjdk-devel && \
    yum -y install createrepo git iproute net-tools openssh-server rpm-build rsync sudo tree svn vim wget && \
    yum -y install ant-junit autoconf bison flex gperf indent libtool && \
    yum clean all

# install all software we need
RUN yum makecache && \
    yum -y install python-paramiko python-devel python-pip python-psutil python-setuptools && \
    yum -y install apr-devel bzip2-devel expat-devel libcurl-devel libevent-devel libuuid-devel && \
    yum -y install libxml2-devel libyaml-devel openssl-devel pam-devel readline-devel snappy-devel && \
    yum -y install apache-ivy libicu perl-ExtUtils-Embed perl-Env perl-JSON && \
    yum -y install json-c-devel libgsasl-devel protobuf-devel && \
    pip install psi lockfile && \
    yum clean all

RUN cd /tmp && wget -q http://curl.haxx.se/download/curl-7.51.0.tar.bz2 && \
    tar -xjf curl-*.tar.bz2 && rm curl-*.tar.bz2 && cd curl-* && \
    ./configure --prefix=/usr --with-gssapi > log-file 2>&1 && make -j4 >> log-file 2>&1 && \
    make install >> log-file 2>&1 && rm -rf /tmp/curl-* && \
    wget -q https://repos.fedorapeople.org/repos/dchen/apache-maven/epel-apache-maven.repo \
    -O /etc/yum.repos.d/epel-apache-maven.repo && yum install -y apache-maven && \
    rm /etc/yum.repos.d/epel-apache-maven.repo && yum clean all

# setup ssh configuration
RUN ssh-keygen -t rsa -N "" -f /root/.ssh/id_rsa && \
    cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys && \
    chmod 0600 /root/.ssh/authorized_keys && \
    echo -e "password\npassword" | passwd 2> /dev/null && \
    { ssh-keyscan localhost; ssh-keyscan 0.0.0.0; } >> /root/.ssh/known_hosts && \
    #
    ssh-keygen -f /etc/ssh/ssh_host_key -N '' -t rsa1 && \
    ssh-keygen -f /etc/ssh/ssh_host_rsa_key -N '' -t rsa && \
    ssh-keygen -f /etc/ssh/ssh_host_dsa_key -N '' -t dsa && \
    sed -i -e 's|Defaults    requiretty|#Defaults    requiretty|' /etc/sudoers && \
    sed -ri 's/UsePAM yes/UsePAM no/g;s/PasswordAuthentication yes/PasswordAuthentication no/g' /etc/ssh/sshd_config && \
    sed -ri 's@^HostKey /etc/ssh/ssh_host_ecdsa_key$@#&@;s@^HostKey /etc/ssh/ssh_host_ed25519_key$@#&@' /etc/ssh/sshd_config

# newer version of gcc and run environment for gpdb
RUN yum -y install centos-release-scl && yum -y install --nogpgcheck cmake3 devtoolset-6-gcc devtoolset-6-gcc-c++ && \
    yum clean all && rm -rf /usr/bin/cmake && ln -s /usr/bin/cmake3 /usr/bin/cmake && \
    echo -e 'source /opt/rh/devtoolset-6/enable' >> /opt/gcc_env.sh && \
    echo -e 'source /opt/gcc_env.sh' >> /root/.bashrc && \
    echo -e 'export JAVA_HOME=/etc/alternatives/java_sdk' >> /root/.bashrc && \
    ldconfig && mkdir -p /usr/local/gpdb/lib/python && \
    cp -r $(find /usr/lib64 -name psi | sort -r | head -1) /usr/local/gpdb/lib/python
