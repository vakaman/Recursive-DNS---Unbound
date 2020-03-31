FROM alpine:latest

### Instala o Unbound / atualiza
RUN apk --no-cache --update add unbound

### Copia as configurações 
COPY unbound.conf /etc/unbound/

### Instala dependencias / Cria pastas / Modifica permissoes
RUN apk add openssl bash curl bind-tools ca-certificates \
  && curl -o /etc/unbound/named.cache https://www.internic.net/domain/named.cache \
  && /usr/sbin/unbound-anchor -a /etc/unbound/root.key | true \
  && mkdir -p /var/log/unbound \
  && chown -R unbound:unbound /etc/unbound \
  && chown -R unbound:unbound /var/log/unbound \
  && chmod 0444 /etc/unbound/root.key \
  && chmod 0444 /etc/unbound/unbound.conf \
  && chmod 0444 /etc/unbound/named.cache \
  && chmod +x /entrypoint.sh \
  && /usr/sbin/unbound-checkconf

### Remove curl
RUN apk del --no-cache curl

EXPOSE 53/udp 53/tcp 

CMD ["/usr/sbin/unbound","-d", "-c","/etc/unbound/unbound.conf"]