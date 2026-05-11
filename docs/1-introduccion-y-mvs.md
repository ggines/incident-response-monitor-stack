# Introducción y MVs
El objetivo de este proyecto es atacar un servidor SSH de una MV víctima para posteriormente extraer sus logs generados hacia Elasticsearch y visualizarlos en
Grafana mediante dashboards.

Entre los objetivos se encuentran:
- Centralizar **métricas y logs** del servidor SSH
- Almacenarlos en **Elasticsearch**, garantizando la persistencia e indexación de evidencias digitales para consultas de alta velocidad
- Visualizarlos en **Grafana mediante dashboards**, transformando datos crudos en inteligencia accionable y KPIs de ataque
- Relacionar los ataques detectados con sus técnicas de **MITRE ATT&CK**
- Proteger el servidor mediante herramientas de seguridad como **Fail2Ban**

## Qué es Elasticsearch
Elasticsearch es un motor de búsqueda y analíticas de código abierto que permite almacenar, buscar y analizar grandes volúmenes de datos de forma rápida y escalable
usando una *API RESTful*, siendo ideal para logs, búsqueda de texto completo, analíticas en tiempo real y búsqueda empresarial.

## Qué es Grafana
Grafana es una herramienta de código abierto para la monitorización y análisis interactivo de métricas y datos en tiempo real, permitiendo crear dashboards dinámicos
y personalizables a partir de diversas fuentes de datos (como Elasticsearch) con el fin de monitorizar infraestructuras, aplicaciones y servicios de forma clara y
tomar decisiones informadas, ofreciendo también funcionalidades de alertas.

## Qué es Filebeat
Filebeat es un agente ligero parte de Elastic Stack que se instala normalmente en servidores para **recopilar y reenviar archivos de logs y otros datos** de manera
centralizada a sistemas como Elasticsearch para su indexación.

## Qué es Metricbeat
Metricbeat es un agente ligero parte de Elastic Stack que se instala normalmente en servidores para **recopilar métricas del sistema operativo** (como CPU, memoria,
disco, red) y de los servicios que se ejecutan en el mismo (como Kubernetes, MySQL, NGINX...) para enviarlas de manera centralizada a sistemas como Elasticsearch.

En este caso, instalaremos Elasticsearch y Grafana en una MV con Ubuntu Server 24.04, y configuraremos otra MV con Ubuntu Server 24.04 para ser monitorizada,
instalando Filebeat y Metricbeat. Por último, también habrá una MV con Kali Linux para realizar los ataques hacia la víctima.

## MVs necesarias

Las MVs son las siguientes:
- **Ubuntu Server 24.04 (Monitor):** Será el servidor que contiene Elasticsearch y Grafana.
  - **CPU:** 4
  - **Memoria:** 8000 MB
  - **Disco:** 80 GB
  - **Red:**
    - Adaptador en Red NAT con la IP fija 10.0.3.10
    - Adaptador host-only con la IP fija 192.168.56.102
   
- **Ubuntu Server 24.04 (Víctima):** Será la máquina para monitorizar y enviar las métricas al servidor.
  - **CPU:** 2
  - **Memoria:** 4096 MB
  - **Disco:** 30 GB
  - **Red:**
    - Adaptador en Red NAT con la IP fija 10.0.3.2
   
- **Kali Linux (Atacante):** Será la maquina desde donde se realizarán los ataques hacia la víctima.
  - **CPU:** 2
  - **Memoria:** 4096 MB
  - **Disco:** 80 GB
  - **Red:**
    - Adaptador en Red NAT con la IP fija 10.0.3.3
   
## Mapa de red
![Mapa de red](/img/mapa-de-red-idr.png)


[Siguiente: Instalación de Elasticsearch en la MV Monitor](2-instalacion-de-elasticsearch-en-la-mv-monitor.md)
