# Security-Onion-NSM-Lab-Implementación-de-Monitoreo-de-Red-y-EDR
Proyecto de diseño y despliegue de una infraestructura de Monitoreo de Seguridad de Red (NSM) y detección de amenazas en un entorno virtualizado controlado

1.  Arquitectura de la Solución: Se realizó un despliegue de Security Onion con formato StandAlone en el cual se usaron contenedores (Docker) para su correcto funcionamiento, en estos contenedores se podían apreciar los diferentes componentes como Suricata (NIDS), Zeek (análisis de tráfico), Elasticsearch, Logstash y Kibana

2.  Stack Tecnológico:

-  Hipervisor: Proxmox VE.
-  SO de Seguridad: Security Onion.
-  Endpoints: Máquinas virtuales Windows y Linux monitoreadas con Elastic Agent.
                        <img width="1000" height="600" alt="image" src="https://github.com/user-attachments/assets/f6ef9a1c-ec96-4f4a-b3df-129e949d100b" />

3.  Específicaciones técnicas:
-  16 núcleos
-  32 GB Memoria RAM
-  2 interfaces de red (1 de monitoreo y otra en modo promiscuo)
                        <img width="940" height="269" alt="image" src="https://github.com/user-attachments/assets/e5a04cbe-d6f8-4204-962f-920a4a8b11cd" />

4.  Objetivos específicos:
-  Implementar la arquitectura base de Security Onion sobre el hipervisor Proxmox VE, optimizando la asignación de recursos de hardware virtual para garantizar la estabilidad de los servicios de Elastic Stack
-  Configurar una arquitectura de despliegue tipo Standalone, centralizando los roles de gestión, búsqueda y sensores de red para consolidar la ingesta de tráfico y logs en un único nodo unificado
-  Simular un entorno de red corporativo mediante el despliegue de máquinas virtuales cliente (Windows/Linux) para generar tráfico de red y telemetría real que alimente el sistema de detección
-  Desplegar y configurar agentes de punto final (Endpoint Agents) en los servidores clientes, aplicando políticas de recolección de logs personalizadas para asegurar la visibilidad completa de eventos del sistema operativo
-  Diseñar tableros de control (Dashboards) y consultas de Threat Hunting personalizadas en Kibana/Security Onion Console, facilitando la interpretación de alertas y la reducción del tiempo de análisis de incidentes
-  Elaborar documentación técnica detallada sobre el proceso de despliegue, configuración y resolución de problemas (troubleshooting), sirviendo como base de conocimiento transferible para futuras implementaciones


5.  Resultados obtenidos:

-  Despliegue de la infraestructura virtual en Proxmox e instalación del sistema operativo Security Onion en modo Standalone.
                        <img width="598" height="449" alt="image" src="https://github.com/user-attachments/assets/93145bc4-cdc2-480d-8162-1434653d5569" />
                        <img width="813" height="453" alt="image" src="https://github.com/user-attachments/assets/37b1fffc-341b-43dd-9457-74ca28db8edd" />

-  Configuración del Bridge de Proxmox en modo promiscuo y ajuste del ageing a 0 (Modo Hub) para replicar tráfico hacia el sensor.
-  Verificación de visibilidad de tráfico en la interfaz de monitoreo mediante tcpdump.
-  Deshabilitación de la validación de checksum (checksum-validation: no) en la configuración de Suricata para evitar el descarte de paquetes por offloading virtual.
-  Corrección del mapeo de interfaces en el archivo Pillar de SaltStack, reasignando el sensor de bond0 a la interfaz física ens19.
-  Validación funcional del NIDS mediante simulación de ataque (curl http://testmyids.com) y confirmación de alertas en la consola.
                        <img width="940" height="256" alt="image" src="https://github.com/user-attachments/assets/db60d173-83b7-4d67-be9e-686c2a6a60ab" />

-  Creación de consultas personalizadas (Queries) y visualizaciones gráficas (Dashboards) en Kibana.
                        <img width="886" height="149" alt="image" src="https://github.com/user-attachments/assets/a922d638-6661-4bef-b85e-f6f732e083e8" />
                        <img width="886" height="355" alt="image" src="https://github.com/user-attachments/assets/96655b61-f91c-4339-857d-f494eced77a5" />

-  Implementación de reglas de supresión para mitigar falsos positivos de herramientas legítimas (Rubeus) en la máquina Kali Linux.
                        <img width="414" height="688" alt="image" src="https://github.com/user-attachments/assets/d2bbd73d-6c34-477c-a0cd-d93eed288197" />
                        
-  Despliegue y enrolamiento de Elastic Agents en los endpoints.
                        <img width="738" height="534" alt="image" src="https://github.com/user-attachments/assets/1340a9d6-4c68-453c-b68f-4d4e17a66512" />
                        <img width="940" height="597" alt="image" src="https://github.com/user-attachments/assets/2f01e00d-1ac4-4974-8b99-fa8084903a5b" />
                        <img width="963" height="366" alt="image" src="https://github.com/user-attachments/assets/0acec9e2-bd1b-42ae-aa44-da713e7fb817" />

-  Configuración de políticas en Elastic Fleet para la ingesta de logs de Windows (Sysmon) y Linux (Auditd).
                        <img width="566" height="466" alt="image" src="https://github.com/user-attachments/assets/faa28777-2fc9-4bb5-a9cf-95d14d00fec4" />
                        <img width="940" height="442" alt="image" src="https://github.com/user-attachments/assets/934cee82-daf6-4dbb-9eb9-3eb9f0702b80" />

-  Activación de la integración "Elastic Defend" y generación de actividad para la creación inicial de índices de EDR.
                       <img width="892" height="426" alt="image" src="https://github.com/user-attachments/assets/822d326b-7d5a-4310-a2da-13e560f1b514" />
                        <img width="940" height="324" alt="image" src="https://github.com/user-attachments/assets/c4f5aea5-ea69-43ea-b83d-9e07e66cab6e" />
-  Optimización de la visualización en Kibana mediante la corrección de métricas de Sysmon, reemplazando la clasificación por severidad (vacía por defecto) por la clasificación basada en Event ID, logrando una interpretación efectiva de la actividad del endpoint.

6.  Desafíos y soluciones:

    a. Visibilidad de Red en Entornos Virtuales (Proxmox)
•	Desafío: La interfaz de monitoreo del sensor no recibía copia del tráfico de la red. El Linux Bridge (vmbr0) de Proxmox actuaba como un switch eficiente, descartando tráfico unicast no destinado al sensor, provocando "ceguera" en el NIDS.
•	Solución: Se implementó una configuración de "Modo Hub" en el bridge virtual. Se habilitó el modo promiscuo y se configuró el parámetro ageing a 0, forzando al switch virtual a inundar (flood) el tráfico a todos los puertos para garantizar la captura total de paquetes.

    b. Integridad de Paquetes y Checksum Offloading
•	Desafío: Suricata descartaba silenciosamente los paquetes capturados antes de analizarlos. Esto se debía a que los drivers de red virtuales (VirtIO) delegan el cálculo de checksum al hipervisor (Offloading), entregando paquetes con sumas de verificación técnicamente inválidas al sistema operativo invitado.
•	Solución: Se modificó la configuración del motor Suricata para deshabilitar la validación estricta de integridad (checksum-validation: no), permitiendo el análisis de paquetes con checksums delegados.

    c. Persistencia de Configuración y Orquestación (SaltStack)
•	Desafío: Las modificaciones manuales en los archivos de configuración (suricata.yaml) eran revertidas automáticamente tras cada reinicio del servicio. Además, existía una discrepancia entre la interfaz lógica por defecto (bond0) y la física real (ens19).
•	Solución: Se identificó y editó el archivo maestro de configuración Pillar (.sls) de SaltStack. Esto permitió persistir los cambios (mapeo de interfaz correcta y variables del motor) y asegurar que el orquestador aplicara la configuración deseada en cada despliegue.

     d. Gestión de Falsos Positivos (Ruido Operativo)
•	Desafío: Herramientas legítimas de pentesting instaladas en la máquina atacante (Kali Linux) generaban alertas críticas de malware (ej. HackTool.Rubeus), saturando la consola de falsos positivos.
•	Solución: Se aplicaron reglas de Supresión (Suppression) específicas basadas en la IP de destino. Esto permitió silenciar las alertas conocidas en la máquina de pruebas sin desactivar la regla globalmente, manteniendo la protección activa para el resto de la red.
