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
  - [Data Source de Filebeat para recopilar logs](docs/8-data-source-de-filebeat-para-recopilar-logs.md)
  - [Data Source de Metricbeat para recopilar métricas del sistema operativo](docs/9-data-source-de-metricbeat-para-recopilar-metricas-del-sistema-operativo.md)
- [Creación de dashboards en Grafana](docs/10-creacion-de-dashboards-en-grafana.md)
  - [Dashboard de seguridad (Filebeat)](docs/11-dashboard-de-seguridad-(filebeat).md)
  - [Dashboard de métricas de rendimiento (Metricbeat)](docs/12-dashboard-de-metricas-de-rendimiento-(metricbeat).md)
- [Protegiendo el servidor SSH de la MV víctima con Fail2ban](docs/13-protegiendo-el-servidor-ssh-de-la-mv-victima-con-fail2ban.md)
  - [Dashboard de Fail2ban en Grafana](docs/14-dashboard-de-fail2ban-en-grafana.md)
- [Conclusiones y webgrafía](docs/15-conclusiones-webgrafia.md)


