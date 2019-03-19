#OTRS ticketing system docker image.
FROM ubuntu:latest
MAINTAINER Pavel Fomichev <p.n.fomichev@gmail.com>
#ENV OTRS_VERSION=6.0.17-01
ENV OTRS_GIT "https://github.com/OTRS/otrs.git"
ENV OTRS_ROOT "/opt/otrs/"
ENV OTRS_BACKUP_DIR "/var/otrs/backups"
#ENV OTRS_CONFIG_DIR "${OTRS_ROOT}Kernel"
ENV OTRS_CONFIG_MOUNT_DIR "/config/"
ENV OTRS_SKINS_MOUNT_DIR "/skins/"
ENV SKINS_PATH "${OTRS_ROOT}/var/httpd/htdocs/skins/"
ENV LANG=en_US.UTF-8
ENV LANGUAGE=en_US.UTF-8

#add user
#USER otrs 
RUN useradd -d /opt/otrs -c 'OTRS user' otrs && usermod -G www-data otrs


RUN apt update && apt install libdigest-md5-perl git \
    libapache2-mod-perl2 libdbd-mysql-perl libtimedate-perl libnet-dns-perl libnet-ldap-perl \
    libio-socket-ssl-perl libpdf-api2-perl libdbd-mysql-perl libsoap-lite-perl libtext-csv-xs-perl \
    libjson-xs-perl libapache-dbi-perl libxml-libxml-perl libxml-libxslt-perl libyaml-perl \
    libarchive-zip-perl libcrypt-eksblowfish-perl libencode-hanextra-perl libmail-imapclient-perl \
    libtemplate-perl -y

# Add scripts and function files
#COPY *.sh /
#RUN mkdir $OTRS_ROOT && cd $OTRS_ROOT && git clone $OTRS_GIT
COPY ./otrs $OTRS_ROOT

#Activate Default Config File
RUN cp $OTRS_ROOT/Kernel/Config.pm.dist $OTRS_ROOT/Kernel/Config.pm

#check perl modules
RUN perl -cw $OTRS_ROOT/bin/cgi-bin/index.pl && perl -cw $OTRS_ROOT/bin/cgi-bin/customer.pl && perl -cw $OTRS_ROOT/bin/otrs.Console.pl


#install apache
RUN apt install apache2 libapache2-mod-perl2 -y


EXPOSE 80
#CMD ["/run.sh"]
