FROM centos:centos7 as base

RUN yum update -y \
    && yum -y install vim wget rpm-build which tar git gcc java-1.8.0-openjdk-devel \
    && yum clean all -y

ENV JAVA_HOME /usr/lib/jvm/java-openjdk
ENV RANGER_HOME /opt/ranger
ENV MAVEN_HOME /opt/maven
ENV RANGER_VERSION 2.1.0
ENV MAVEN_VERSION 3.6.3

ENV MAVEN_OPTS "-Xmx2048m -XX:MaxMetaspaceSize=512m"

RUN mkdir $MAVEN_HOME \
    && export http_proxy=$http_proxy \
    && wget -P $MAVEN_HOME http://mirror.dsrg.utoronto.ca/apache/maven/maven-3/$MAVEN_VERSION/binaries/apache-maven-$MAVEN_VERSION-bin.tar.gz \
    && tar -xzvf $MAVEN_HOME/apache-maven-$MAVEN_VERSION-bin.tar.gz -C $MAVEN_HOME \
    && rm -f $MAVEN_HOME/apache-maven-$MAVEN_VERSION-bin.tar.gz

# Or you can use the binary from the official site: https://ranger.apache.org/download.html (currently no pre-built available)
RUN cd /opt \
    && git clone --depth 1 --branch release-ranger-$RANGER_VERSION https://github.com/apache/ranger.git \
    && cd ranger \
    && $MAVEN_HOME/apache-maven-$MAVEN_VERSION/bin/mvn -T 10 -DskipTests clean compile package install assembly:assembly \
    #&& wget -O plugin-sampleapp.xml https://raw.githubusercontent.com/apache/ranger/2144cb738000ddc9086cb0ab5db5fcc42e337a2c/ranger-examples/distro/src/main/assembly/plugin-sampleapp.xml \
    #&& wget -O plugin-sampleapp.xml https://raw.githubusercontent.com/apache/ranger/2144cb738000ddc9086cb0ab5db5fcc42e337a2c/ranger-examples/distro/src/main/assembly/sampleapp.xml \

    && rm -rf ~/.m2 \
    && ls | grep -v target | xargs rm -rf \
    && rm -rf target/*.zip


FROM base

ENV MYSQL_CONNECTOR_JAVA_VERSION 5.1.41

RUN yum update -y \
    && yum -y install mysql \
    && yum clean all -y

RUN wget -P /opt https://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-$MYSQL_CONNECTOR_JAVA_VERSION.tar.gz \
    && tar -zxvf /opt/mysql-connector-java-$MYSQL_CONNECTOR_JAVA_VERSION.tar.gz -C /opt \
    && rm -f /opt/mysql-connector-java-$MYSQL_CONNECTOR_JAVA_VERSION.tar.gz \
    && ln -s /opt/mysql-connector-java-$MYSQL_CONNECTOR_JAVA_VERSION/mysql-connector-java-$MYSQL_CONNECTOR_JAVA_VERSION-bin.jar /opt/mysql-connector-java.jar
