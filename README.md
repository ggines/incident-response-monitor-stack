# Monitorización y respuesta ante incidentes con Grafana, Elasticsearch y Fail2ban
En este proyecto se explica paso a paso la implementación de un sistema de monitorización e IR en un entorno virtualizado con VirtualBox.
Este sistema está compuesto por herramientas open source, siendo estas Grafana, Elasticsearch y Fail2ban.

![Portada](img/cover-image.jpg)

### Tabla de contenidos
- [Introducción y MVs](docs/1-introduccion-y-mvs.md)
- [Instalación de Elasticsearch en la MV Monitor](docs/2-instalacion-de-elasticsearch-en-la-mv-monitor.md)
- [Configuración de Elasticsearch](docs/3-configuracion-de-elasticsearch.md)
- [Instalación de Grafana en la MV Monitor](docs/4-instalacion-de-grafana-en-la-mv-monitor.md)
- [Instalación de Metricbeat en la MV víctima](docs/5-instalacion-de-metricbeat-en-la-mv-victima.md)
- [Instalación de Filebeat en la MV víctima](docs/6-instalacion-de-filebeat-en-la-mv-victima.md)
- [Conectando Grafana con Elasticsearch](docs/7-conectando-grafana-con-elasticsearch.md)
  - [Data Source de Filebeat para recopilar logs](docs/7-conectando-grafana-con-elasticsearch.md#data-source-de-filebeat-para-recopilar-logs)
  - [Data Source de Metricbeat para recopilar métricas del sistema operativo](docs/7-conectando-grafana-con-elasticsearch.md#data-source-de-metricbeat-para-recopilar-métricas-del-sistema-operativo)
- [Creación de dashboards en Grafana](docs/8-creacion-de-dashboards-en-grafana.md)
  - [Dashboard de seguridad (Filebeat)](docs/8-creacion-de-dashboards-en-grafana.md#dashboard-de-seguridad-filebeat)
  - [Dashboard de métricas de rendimiento (Metricbeat)](docs/8-creacion-de-dashboards-en-grafana.md#dashboard-para-métricas-de-rendimiento-metricbeat)
- [Protegiendo el servidor SSH de la MV víctima con Fail2ban](docs/9-protegiendo-el-servidor-ssh-de-la-mv-victima-con-fail2ban.md)
  - [Dashboard de Fail2ban en Grafana](docs/9-protegiendo-el-servidor-ssh-de-la-mv-victima-con-fail2ban.md#dashboard-de-fail2ban-en-grafana)
- [Conclusiones y webgrafía](docs/10-conclusiones-y-webgrafia.md)

### Documentación en PDF 📄
La documentación también se puede descargar en [formato PDF](https://github.com/ggines/incident-response-monitor-stack/blob/main/docs/Monitorizacion-Incidentes-Grafana-Elasticsearch-Fail2ban.pdf).


