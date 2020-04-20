FROM alpine:3.11

### Instala o Unbound / atualiza
RUN apk add unbound

### Copia as configurações 
COPY unbound.conf /etc/unbound/

### Instala dependencias / Cria pastas / Modifica permissoes
RUN apk add openssl bash curl bind-tools ca-certificates dnstop

RUN curl -o /etc/unbound/named.cache https://www.internic.net/domain/named.cache 

RUN /usr/sbin/unbound-anchor -a /etc/unbound/root.key | true 

RUN chown -R unbound:unbound /etc/unbound \
  && chmod 0444 /etc/unbound/root.key \
  && chmod 0444 /etc/unbound/unbound.conf \
  && chmod 0444 /etc/unbound/named.cache

### Check syntax errors
RUN /usr/sbin/unbound-checkconf

### test if DNSSEC is working
RUN /usr/sbin/unbound-host -C /etc/unbound/unbound.conf -v sigok.verteiltesysteme.net

### Remove curl
RUN apk del curl

EXPOSE 53/udp 53/tcp 

CMD ["/usr/sbin/unbound","-d", "-c","/etc/unbound/unbound.conf"]