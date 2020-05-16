FROM alpine:3.11

### Instala dependencias
RUN apk add unbound tzdata sudo openssl bash curl bind-tools ca-certificates dnstop

ARG TZ='America/Sao_Paulo'

### Copia as configurações
COPY unbound.conf /etc/unbound/

### Get latest name.cache from internic
RUN curl -o /etc/unbound/named.cache https://www.internic.net/domain/named.cache

### Generate root.key
RUN /usr/sbin/unbound-anchor -a /etc/unbound/root.key | true

### Create
RUN mkdir /var/log/unbound \
  && touch /var/log/unbound/unbound.log \
  && cp /usr/share/zoneinfo/${TZ} /etc/localtime

RUN ln -sf /proc/1/fd/1 /var/log/unbound/unbound.log

### Generate keys
RUN /usr/sbin/unbound-control-setup -d /etc/unbound

### Check syntax errors
RUN /usr/sbin/unbound-checkconf

### Chown
RUN chown -R unbound:unbound /etc/unbound \
  && chown -R unbound:unbound /etc/unbound/root.key \
  && chown unbound:unbound /var/log/unbound/unbound.log

### Chmod
RUN chmod 0444 /etc/unbound/root.key \
  && chmod 0444 /etc/unbound/unbound.conf \
  && chmod 0444 /etc/unbound/named.cache

### test if DNSSEC is working
RUN /usr/sbin/unbound-host -C /etc/unbound/unbound.conf -v internic.net

### Remove curl / tzdata
RUN apk del curl tzdata

EXPOSE 53/udp 53/tcp

CMD ["/usr/sbin/unbound","-d", "-c","/etc/unbound/unbound.conf"]
