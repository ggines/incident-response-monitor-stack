# Monitorización y respuesta ante incidentes con Grafana, Elasticsearch y Fail2ban
En este proyecto se explica paso a paso la implementación de un sistema de monitorización e IR en un entorno virtualizado con VirtualBox.
Este sistema está compuesto por herramientas open source, siendo estas Grafana, Elasticsearch y Fail2ban.

![Portada](img/cover-image.jpg)

### Tabla de contenidos
- [Introducción y MVs](docs/introduccion-y-mvs)
- [Instalación de Elasticsearch en la MV Monitor](docs/instalacion-de-elasticsearch-en-la-mv-monitor)
- [Configuración de Elasticsearch](docs/configuracion-de-elasticsearch)
- [Instalación de Grafana en la MV Monitor](docs/instalacion-de-grafana-en-la-mv-monitor)
- [Instalación de Metricbeat en la MV víctima](docs/instalacion-de-metricbeat-en-la-mv-victima)
- [Instalación de Filebeat en la MV víctima](docs/instalacion-de-filebeat-en-la-mv-victima)
- [Conectando Grafana con Elasticsearch](docs/conectando-grafana-con-elasticsearch)
  - [Data Source de Filebeat para recopilar logs](docs/data-source-de-filebeat-para-recopilar-logs)
  - [Data Source de Metricbeat para recopilar métricas del sistema operativo](docs/data-source-de-metricbeat-para-recopilar-metricas-del-sistema-operativo)
- [Creación de dashboards en Grafana](docs/creacion-de-dashboards-en-grafana)
  - [Dashboard de seguridad (Filebeat)](docs/dashboard-de-seguridad-(filebeat))
  - [Dashboard de métricas de rendimiento (Metricbeat)](docs/dashboard-de-metricas-de-rendimiento-(metricbeat))
- [Protegiendo el servidor SSH de la MV víctima con Fail2ban](docs/protegiendo-el-servidor-ssh-de-la-mv-victima-con-fail2ban)
  - [Dashboard de Fail2ban en Grafana](docs/dashboard-de-fail2ban-en-grafana)
- [Conclusiones](docs/conclusiones)
- [Webgrafía](docs/webgrafía)

