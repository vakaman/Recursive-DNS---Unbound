FROM alpine:3.11

### Instala o Unbound / atualiza
RUN apk add unbound tzdata 

ARG TZ='America/Sao_Paulo'

### Copia as configurações 
COPY unbound.conf /etc/unbound/

### Instala dependencias / Cria pastas / Modifica permissoes
RUN apk add sudo openssl bash curl bind-tools ca-certificates dnstop zabbix-agent

RUN curl -o /etc/unbound/named.cache https://www.internic.net/domain/named.cache 

RUN /usr/sbin/unbound-anchor -a /etc/unbound/root.key | true 

### Create 
RUN touch /etc/unbound/unbound.log \
  && cp /usr/share/zoneinfo/${TZ} /etc/localtime

### Chown
RUN chown -R unbound:unbound /etc/unbound \
  && chown -R unbound:unbound /etc/unbound/root.key \
  && chown unbound:unbound /etc/unbound/unbound.log

### Chmod
RUN chmod 0444 /etc/unbound/root.key \
  && chmod 0444 /etc/unbound/unbound.conf \
  && chmod 0444 /etc/unbound/named.cache
  

### Check syntax errors
RUN /usr/sbin/unbound-checkconf

### test if DNSSEC is working
RUN /usr/sbin/unbound-host -C /etc/unbound/unbound.conf -v internic.net

### Remove curl / tzdata
RUN apk del curl tzdata

EXPOSE 53/udp 53/tcp 

CMD ["/usr/sbin/unbound","-d", "-c","/etc/unbound/unbound.conf"]