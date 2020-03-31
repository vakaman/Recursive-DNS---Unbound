FROM alpine:latest
MAINTAINER Maicol Kaiser <maicolkaiser.oliveira@gmail.com>

## Instala o bind
RUN apk --no-cache add bind bind-tools &&\

  ## Apaga o usuário named
  deluser named &&\

  ## Cria o grupo "named"
  addgroup -g 505 named &&\

  ## Adiciona um usuário com o diretório /etc/bind
  ## Adiciona o usuário no grupo 505
  adduser -h /etc/bind -D -u 505 -g named -G named -s /sbin/nologin named &&\

  ## Muda o grupo das pastas, para o grupo "named"
  chgrp named /etc/bind /var/bind /var/run/named /var/bind/dyn /var/bind/pri /var/bind/sec

# set the recursive default for the config; listen on any interface; enable prefetch for expiring records

COPY named.conf /etc/bind/named.conf.recursive

RUN ln -s /etc/bind/named.conf.recursive /etc/bind/named.conf

## Criando diretório de logs
RUN mkdir /var/log/bind
RUN chown -R named:named /var/log/bind

RUN chown -R named:named /var/bind/named.ca
RUN chown -R named:named /var/bind/root.cache
RUN chown -R named:named /etc/bind/rndc.key

#  sed -i '/listen-on { 127.0.0.1; };/s/^/\/\//g' /etc/bind/named.conf.recursive &&\
#  sed -i '/127.0.0.1\/32/s//0.0.0.0\/0/g' /etc/bind/named.conf.recursive &&\
#  sed -i '/^options.*/a prefetch 2 9;' /etc/bind/named.conf.recursive &&\
#  named-checkconf /etc/bind/named.conf

COPY entrypoint.sh /entrypoint.sh

EXPOSE 53 53/udp
ENTRYPOINT ["/entrypoint.sh"]
CMD ["/usr/sbin/named","-c","/etc/bind/named.conf","-f","-u","named","-g","-4"]
