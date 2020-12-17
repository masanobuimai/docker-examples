FROM openjdk:8-jdk

# OSの日本語化
RUN apt-get update && \
    apt-get install locales -y && \
    sed -i -E 's/# (ja_JP.UTF-8)/\1/' /etc/locale.gen && \
    locale-gen && \
    update-locale LANG=ja_JP.UTF-8 && \
    localedef -i ja_JP -c -f UTF-8 -A /usr/share/locale/locale.alias ja_JP.UTF-8
ENV LANG ja_JP.UTF-8
ENV TZ 'Asia/Tokyo'

# vimのインストール
RUN apt-get install vim -y

# tomcatのインストール
RUN curl -O https://archive.apache.org/dist/tomcat/tomcat-8/v8.5.61/bin/apache-tomcat-8.5.61.tar.gz
RUN tar zxf apache-tomcat-8.5.61.tar.gz && \
    mv apache-tomcat-8.5.61 /opt/tomcat && \
    chmod +x /opt/tomcat/bin/*.sh

COPY tomcat/conf/ /opt/tomcat/conf

CMD ["/opt/tomcat/bin/catalina.sh", "jpda", "run" ]