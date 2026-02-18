# Security-Onion-NSM-Lab-Implementación-de-Monitoreo-de-Red-y-EDR
Proyecto de diseño y despliegue de una infraestructura de Monitoreo de Seguridad de Red (NSM) y detección de amenazas en un entorno virtualizado controlado

Arquitectura de la Solución: Se realizó un despliegue de Security Onion con formato StandAlone en el cual se usaron contenedores (Docker) para su correcto funcionamiento, en estos contenedores se podían apreciar los diferentes componentes como Suricata (NIDS), Zeek (análisis de tráfico), Elasticsearch, Logstash y Kibana Stack Tecnológico:

-  Hipervisor: Proxmox VE.
-  SO de Seguridad: Security Onion.
-  Endpoints: Máquinas virtuales Windows y Linux monitoreadas con Elastic Agent.
Específicaciones técnicas:
-  16 núcleos
-  32 GB Memoria RAM
-  2 interfaces de red (1 de monitoreo y otra en modo promiscuo)
