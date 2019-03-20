#OTRS ticketing system docker image.
# Admin manuals https://doc.otrs.com/doc/manual/admin/6.0/en/html/manual-installation-of-otrs.html
FROM ubuntu:latest
MAINTAINER Pavel Fomichev <p.n.fomichev@gmail.com>

ENV DEBIAN_FRONTEND noninteractive

ENV OTRS_GIT "https://github.com/OTRS/otrs.git"

ENV OTRS_ROOT "/opt/otrs/"
ENV OTRS_CONFIG_DIR "${OTRS_ROOT}Kernel"
#ENV OTRS_CONFIG_MOUNT_DIR "/config/"
#ENV OTRS_SKINS_MOUNT_DIR "/skins/"
#ENV SKINS_PATH "${OTRS_ROOT}/var/httpd/htdocs/skins/"
#ENV OTRS_BACKUP_DIR "/var/otrs/backups"

ENV LANG=en_US.UTF-8
ENV LANGUAGE=en_US.UTF-8
ENV LC_ALL=en_US.UTF-8

#add OTRS user
RUN useradd -d ${OTRS_ROOT} -c 'OTRS user' otrs && usermod -G www-data otrs

RUN apt update
RUN apt install apt-utils locales -y
#fix locale
RUN /usr/sbin/locale-gen "en_US.UTF-8" && echo "locales changed"

RUN apt install libdigest-md5-perl git \
    libapache2-mod-perl2 libdbd-mysql-perl libtimedate-perl libnet-dns-perl libnet-ldap-perl \
    libio-socket-ssl-perl libpdf-api2-perl libdbd-mysql-perl libsoap-lite-perl libtext-csv-xs-perl \
    libjson-xs-perl libapache-dbi-perl libxml-libxml-perl libxml-libxslt-perl libyaml-perl \
    libarchive-zip-perl libcrypt-eksblowfish-perl libencode-hanextra-perl libmail-imapclient-perl \
    libtemplate-perl libcrypt-ssleay-perl libdatetime-perl libterm-ui-perl -y

# Add scripts and function files
#COPY *.sh /

# get fresh OTRS version
#RUN mkdir $OTRS_ROOT && cd $OTRS_ROOT && git clone $OTRS_GIT
COPY ./otrs ${OTRS_ROOT}

#  Install Additional Perl Modules
RUN perl ${OTRS_ROOT}bin/otrs.CheckModules.pl

#Activate Default Config File
RUN cp ${OTRS_ROOT}Kernel/Config.pm.dist ${OTRS_ROOT}Kernel/Config.pm

#check perl modules
RUN perl -cw ${OTRS_ROOT}bin/cgi-bin/index.pl && perl -cw ${OTRS_ROOT}bin/cgi-bin/customer.pl && perl -cw ${OTRS_ROOT}bin/otrs.Console.pl


#install apache
RUN apt install apache2 libapache2-mod-perl2 -y

#configure apache
RUN ln -s ${OTRS_ROOT}scripts/apache2-httpd.include.conf /etc/apache2/sites-enabled/zzz_otrs.conf

#list apache modules
#RUN apachectl -M 

#enable apache modules
RUN a2enmod perl && a2enmod deflate && a2enmod filter && a2enmod headers

#restart apache
RUN service apache2 restart

#file permitions
RUN cd ${OTRS_ROOT} && bin/otrs.SetPermissions.pl

EXPOSE 80
#CMD ["/run.sh"]
