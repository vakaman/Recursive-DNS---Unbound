FROM tiredofit/alpine:3.11

### Instala o Unbound / atualiza / unbound tzdata zabbix-agent openrc
RUN apk add unbound tzdata zabbix-agent openrc

ARG TZ='America/Sao_Paulo'

VOLUME ["/sys/fs/cgroup"]

RUN mkdir /etc/zabbix/zabbix_agentd.conf.d

### Copia as configurações 
COPY unbound.conf /etc/unbound/
COPY zabbix_agent.conf /etc/zabbix/zabbix_agentd.conf
COPY zabbix_agent-sudoers /etc/sudoers.d/zabbix-agent
COPY userparameter_unbound.conf /etc/zabbix/zabbix_agentd.conf.d/

### Instala dependencias / Cria pastas / Modifica permissoes
RUN apk add sudo openssl bash curl bind-tools ca-certificates dnstop zabbix-agent

RUN curl -o /etc/unbound/named.cache https://www.internic.net/domain/named.cache 

RUN /usr/sbin/unbound-anchor -a /etc/unbound/root.key | true 

### Create 
RUN touch /etc/unbound/unbound.log \
  && cp /usr/share/zoneinfo/${TZ} /etc/localtime

### Generate keys
RUN /usr/sbin/unbound-control-setup -d /etc/unbound 

### Check syntax errors
RUN /usr/sbin/unbound-checkconf

### Chown
RUN chown -R unbound:unbound /etc/unbound \
  && chown -R unbound:unbound /etc/unbound/root.key \
  && chown unbound:unbound /etc/unbound/unbound.log

### Chmod
RUN chmod 0444 /etc/unbound/root.key \
  && chmod 0444 /etc/unbound/unbound.conf \
  && chmod 0444 /etc/unbound/named.cache

### test if DNSSEC is working
RUN /usr/sbin/unbound-host -C /etc/unbound/unbound.conf -v internic.net

RUN rc-update add zabbix-agentd

#RUN /etc/init.d/zabbix-agentd start

### Remove curl / tzdata
RUN apk del curl tzdata

EXPOSE 53/udp 53/tcp 

CMD ["/usr/sbin/unbound","-d", "-c","/etc/unbound/unbound.conf"]