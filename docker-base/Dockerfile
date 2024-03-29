FROM centos:7
#
# Base Image for Paxata Services
#
ENV JAVA_VERSION 8u191
ENV LINK_PATH /usr/local/paxata/server

# Set the locale(en_US.UTF-8) 
ENV LANGUAGE en_US:en 

ENV LANG en_US.UTF-8 
ENV LC_ALL en_US.UTF-8

ENV S3_URL https://s3-us-west-2.amazonaws.com
ENV S3_BUCKET_NAME paxata-filemirror
ENV JDK_DOWNLOAD_SHA256  cefde0b2f2cbbf0441243c78dad863e6e06c91dcfaeaea8df704ce5fb80628f1
ENV GOSU_DOWNLOAD_SHA256 e9eee45f229fc81acf6a32b8b87105e8739247eb93132a472b8d2baa2b98cb3c

# install dependencies
RUN yum clean all && \
    yum install -y krb5-libs \
        krb5-workstation \
        unzip \
        tar \
        wget \
        which \
        yum-plugin-ovl && \
    yum clean all && \
    localedef --quiet -c -i en_US -f UTF-8 en_US.UTF-8

# Install Java
RUN wget -c --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u191-b12/2787e4a523244c269598db4e85c51e0c/jdk-8u191-linux-x64.rpm && \
    echo "$JDK_DOWNLOAD_SHA256 *jdk-$JAVA_VERSION-linux-x64.rpm" | sha256sum -c - && \
    rpm -Uvh jdk-$JAVA_VERSION-linux-x64.rpm && \
    alternatives --install /usr/bin/java java /usr/java/latest/bin/java 2 && \
    rm -f jdk-$JAVA_VERSION-linux-x64.rpm && \
    yum clean all && \
    rm -Rf /var/cache/yum

# Java Environment Variables
ENV JAVA_HOME /usr/java/latest
ENV JRE_HOME $JAVA_HOME/jre
ENV CLASSPATH .:$JAVA_HOME/lib:$JRE_HOME/lib:$CLASSPATH
ENV PATH $JAVA_HOME/bin:$JRE_HOME/bin:$PATH

# Install gosu
# the sha256sum requires two spaces between the checksum and binary.
# http://unix.stackexchange.com/questions/139891/why-does-verifying-sha256-checksum-with-sha256sum-fail-on-debian-and-work-on-u
# This might cause an error in Centos6 
RUN curl -fSL -o /usr/local/sbin/gosu $S3_URL/$S3_BUCKET_NAME/gosu-amd64 && \
    echo "$GOSU_DOWNLOAD_SHA256  /usr/local/sbin/gosu" | sha256sum -c - && \
    chmod 0755 /usr/local/sbin/gosu

# Add required files
COPY files/wait-for-it.sh /usr/sbin/
COPY files/krb5.conf /etc/
COPY files/local_policy.jar $JRE_HOME/lib/security/local_policy.jar
COPY files/US_export_policy.jar $JRE_HOME/lib/security/US_export_policy.jar

# Change permission
RUN chmod 755 /usr/sbin/wait-for-it.sh
