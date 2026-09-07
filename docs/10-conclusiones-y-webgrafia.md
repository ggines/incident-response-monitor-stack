# ✔️ Conclusiones
Este proyecto me ha permitido profundizar en el uso de **Grafana** como herramienta de
monitorización, junto con **Elasticsearch** como motor de búsqueda y análisis de datos.

He aprendido a conectar Grafana con Elasticsearch como fuente de datos y a
implementar agentes como **Filebeat** y **Metricbeat** para centralizar registros y métricas
del sistema operativo. A partir del archivo ```/var/log/auth.log``` y mediante Filebeat, he
podido monitorear eventos relacionados con intentos de inicio de sesión y con el
servicio SSH.

Por su parte, Metricbeat me ha permitido obtener métricas en tiempo real
del sistema, como el tráfico de red y el consumo de memoria.
También he aplicado medidas de hardening utilizando Fail2ban para proteger servicios
críticos (SSH en este caso) y evitar posibles intrusiones. Posteriormente, he
configurado Filebeat para monitorizar también los logs de Fail2ban, integrándolos en
Grafana y permitiendo su visualización en paneles personalizados.

Como conclusión, la integración de Grafana con Elasticsearch, junto con agentes
como Filebeat y Metricbeat, demuestra que es posible crear un sistema de
monitorización centralizado y orientado a la seguridad, funcionando como una
alternativa ligera a un SIEM para supervisar eventos y métricas relevantes del sistema

# 📘 Webgrafía
Fuentes consultadas para realizar este proyecto:

- [Install Elasticsearch with a Debian package | Elastic Docs](https://www.elastic.co/docs/deploy-manage/deploy/self-managed/install-elasticsearch-with-debian-package#deb-repo) - *Elastic Docs*
- [Install Grafana | Grafana documentation](https://grafana.com/docs/grafana/latest/setup-grafana/installation/) - *Grafana documentation*
- [Metricbeat con Elasticsearch 8.x - Parte 1: Instalación y protección](https://evermight.com/es/instalacion-de-metricbeat-parte-1) - *Evermight*
- [Repositories for APT and YUM (Metricbeat)](https://www.elastic.co/docs/reference/beats/metricbeat/setup-repositories) - *Elastic Docs*
- [Repositories for APT and YUM (Filebeat)](https://www.elastic.co/docs/reference/beats/filebeat/setup-repositories) - *Elastic Docs*
