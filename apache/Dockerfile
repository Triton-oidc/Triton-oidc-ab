#Download base image ubuntu 16.04
FROM centos

# Update Software repository
#RUN apt-get update

ARG proxy=myproxy:8080
ARG publichostname=server12.tpsarctriton.sbx-aas.gotocloud.io
ARG listeningport=443
ARG httpmode=https
ARG wellknown=https://idp01.tpsarctriton.sbx-aas.gotocloud.io:9443/fake/auth/realms/GreenAirline/.well-known/openid-configuration
#ARG wellknown=https://idp01.tpsarctriton.sbx-aas.gotocloud.io:9443/auth/realms/GreenAirline/.well-known/openid-configuration
ARG clientid=greenairlines-secure
ARG clientsecret=da0e405b-d12a-4380-bc9b-67fcd9f521a0
ARG redirecturi=https://${publichostname}:443/callback
#Not an argument, c'estaen dure dans le fichier de conf
#pas reussi a faire passer l'espace ni les doubles quotes
#ARG oidcresponcetype=id_token\ token
ARG redirectafteroidc=http://localhost:80

#wellknown##
#clientiD##
#clientsecret##
#redirecturi##
#oidc responce type##
#proxy##
#redirectafteroidc##
#rediretafteroidc##


ENV http_proxy ${proxy}
ENV https_proxy ${proxy}

RUN yum update && \
yum clean all && \
yum install -y httpd mod_ssl && \
yum clean all


#ADD 000-default.conf	/etc/httpd/conf.d/
ADD 010-resource.conf		/etc/httpd/conf.d/
ADD index.html			/var/www/html/
ADD jks-pub-key.pem		/etc/pki/tls/certs/

#Le fichier avec les cles pour la signature du JKS
ADD certs			/var/www/html/static-certs

RUN yum install -y wget openssl jansson pkgconfig && \
wget https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm && \
wget https://mod-auth-openidc.org/download/cjose-0.5.1-1.el7.centos.x86_64.rpm && \
wget https://github.com/zmartzone/mod_auth_openidc/releases/download/v2.3.5/mod_auth_openidc-2.3.5-1.el7.x86_64.rpm && \
yum install -y epel-release-latest-7.noarch.rpm && \
yum install -y hiredis && \
yum install -y localinstall cjose-0.5.1-1.el7.centos.x86_64.rpm  && \
yum install -y localinstall mod_auth_openidc-2.3.5-1.el7.x86_64.rpm && \
yum clean all && \
echo -e "\n#Manual add\nLoadModule auth_openidc_module /usr/lib64/httpd/modules/mod_auth_openidc.so" >> /etc/httpd/conf/httpd.conf

ADD ssl.conf /etc/httpd/conf.d/
ADD ssl-cert-json.conf /etc/httpd/conf.d/


RUN sed -i 's/##clientiD##/'${clientid}'/g  ;  s/##clientsecret##/'${clientsecret}'/g  ;  s|##redirecturi##|'${redirecturi}'|g  ; s/##publichostname##/'${publichostname}'/g ; s/##listeningport##/'${listeningport}'/g ; s/##httpmode##/'${httpmode}'/g ; s|##proxy##|'${proxy}'|g ; s|##redirectafteroidc##|'${redirectafteroidc}'|g ; s|##wellknown##|'${wellknown}'|g    ' /etc/httpd/conf.d/ssl.conf 

#Pour debuging tools
#RUN yum install -y net-tools vim mlocate && \
#updatedb

#important sinon il va utiliser le proxy pour sortir
ENV http_proxy ""
ENV https_proxy ""
# s/##oidcresponcetype##/'${oidcresponcetype}'/g ;

#RUN yum install -y openssl jansson pkgconfig
#RUN rpm -ivh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
#RUN rpm -ivh https://github.com/zmartzone/mod_auth_openidc/releases/download/v2.3.5/mod_auth_openidc-2.3.5-1.el7.x86_64.rpm

EXPOSE 80 443
#CMD /bin/bash

#start the server
CMD ["-D", "FOREGROUND"]
ENTRYPOINT ["/usr/sbin/httpd"]
