# Security-Onion-NSM-Lab: Implementación de Monitoreo de Red y EDR

Proyecto de diseño y despliegue de una infraestructura de Monitoreo de Seguridad de Red (NSM) y detección de amenazas en un entorno virtualizado controlado.

---

### 1. Arquitectura de la Solución
Se realizó un despliegue de **Security Onion** en formato **Standalone**, utilizando una arquitectura basada en contenedores (**Docker**) para garantizar la modularidad y eficiencia de los servicios. Los componentes core integrados incluyen:
* **Suricata:** Motor de detección de intrusiones de red (NIDS).
* **Zeek:** Analizador de protocolos y generación de metadata de red.
* **Elastic Stack (ELK):** Elasticsearch, Logstash y Kibana para el almacenamiento, procesamiento y visualización de eventos.

### 2. Stack Tecnológico

| Capa | Tecnología |
| :--- | :--- |
| **Hipervisor** | Proxmox VE |
| **Sistema Operativo SOC** | Security Onion 2.4 |
| **Telemetría de Endpoints** | Elastic Agent (Windows/Linux) |

<p align="center">
  <img src="https://github.com/user-attachments/assets/f6ef9a1c-ec96-4f4a-b3df-129e949d100b" width="70%" alt="Arquitectura de Red">
  <br>
  <em> Topología lógica del Laboratorio en el entorno Proxmox.</em>
</p>

### 3. Especificaciones Técnicas del Nodo
Para asegurar la estabilidad del **Elastic Stack** y el procesamiento en tiempo real, se asignaron los siguientes recursos:
* **CPU:** 16 núcleos físicos/virtuales.
* **RAM:** 32 GB (Optimizado para el heap de Elasticsearch).
* **Interfaces de Red:** 1. Interfaz de Gestión (Management).
    2. Interfaz de Monitoreo (Modo promiscuo para captura de tráfico).

<p align="center">
  <img src="https://github.com/user-attachments/assets/e5a04cbe-d6f8-4204-962f-920a4a8b11cd" width="60%">
</p>

---

### 4. Objetivos del Proyecto
* **Optimización de Recursos:** Implementar Security Onion sobre Proxmox VE garantizando estabilidad.
* **Centralización NSM:** Consolidar roles de gestión, búsqueda y sensores en un único nodo Standalone.
* **Simulación Corporativa:** Generar telemetría real mediante máquinas cliente Windows/Linux.
* **Visibilidad de Endpoint:** Desplegar Elastic Agents con políticas personalizadas.
* **Inteligencia de Datos:** Diseñar Dashboards y consultas de Threat Hunting en Kibana.
* **Transferencia de Conocimiento:** Documentar procesos de troubleshooting y despliegue.

---

### 5. Resultados Obtenidos e Implementación Técnica
<p align="center">
  <img src="https://github.com/user-attachments/assets/301497ed-fe8e-4fd7-980f-424b14171fb1" width="60%">
  <br>
  <em>Flujo de trabajo del analista</em>
</p>


#### A. Despliegue de Infraestructura y Visibilidad de Red
Se configuró el bridge virtual de Proxmox para eliminar la "ceguera" del sensor, permitiendo que el tráfico unicast de la red fuera visible para el NIDS.

| Estado del Sistema | Configuración de Red |
| :---: | :---: |
| <img src="https://github.com/user-attachments/assets/93145bc4-cdc2-480d-8162-1434653d5569" width="350"> | <img src="https://github.com/user-attachments/assets/37b1fffc-341b-43dd-9457-74ca28db8edd" width="350"> |
| *Verificación de servicios activos en el nodo.* | *Ajuste de modo promiscuo en Proxmox.* |

#### B. Detección de Intrusiones (NIDS) y Threat Hunting
Se validó la capacidad de respuesta mediante ataques simulados. Se optimizó la visualización en Kibana corrigiendo métricas de **Sysmon**, reemplazando la clasificación por severidad vacía por una basada en **Event ID**.

<p align="center">
  <img src="https://github.com/user-attachments/assets/db60d173-83b7-4d67-be9e-686c2a6a60ab" width="85%">
  <br>
  <em><strong>Evidencia:</strong> Alerta generada tras simular ataque con testmyids.</em>
</p>

| Dashboards Personalizados | Consultas (Queries) de Hunting |
| :---: | :---: |
| <img src="https://github.com/user-attachments/assets/96655b61-f91c-4339-857d-f494eced77a5" width="400"> | <img src="https://github.com/user-attachments/assets/a922d638-6661-4bef-b85e-f6f732e083e8" width="400"> |

#### C. Gestión de Endpoints y EDR (Elastic Defend)
Se centralizó la administración de la seguridad de los endpoints mediante **Elastic Fleet**, activando la integración **Elastic Defend** para capacidades de EDR.

<p align="center">
  <img src="https://github.com/user-attachments/assets/822d326b-7d5a-4310-a2da-13e560f1b514" width="80%">
  <br>
  <em>Gestión centralizada de activos y políticas de protección EDR.</em>
</p>

| Telemetría de Agentes | Políticas de Ingesta |
| :---: | :---: |
| <img src="https://github.com/user-attachments/assets/1340a9d6-4c68-453c-b68f-4d4e17a66512" width="400"> | <img src="https://github.com/user-attachments/assets/934cee82-daf6-4dbb-9eb9-3eb9f0702b80" width="400"> |

#### D. Afinamiento y Reducción de Ruido
Se implementaron reglas de supresión para mitigar falsos positivos originados por herramientas legítimas de auditoría (ej. Rubeus en Kali Linux).

<p align="center">
  <img src="https://github.com/user-attachments/assets/d2bbd73d-6c34-477c-a0cd-d93eed288197" width="250">
  <br>
  <em>Lógica de exclusión para herramientas de Pentesting.</em>
</p>

---

### 6. Desafíos y Soluciones Técnicas

#### Visibilidad de Red en Proxmox
* **Desafío:** El Linux Bridge (`vmbr0`) actuaba como un switch eficiente, impidiendo que el sensor capturara tráfico destinado a otros hosts.
* **Solución:** Configuración de **Modo Hub**. Se habilitó el modo promiscuo y se estableció el parámetro `ageing` a `0`, forzando la inundación de tráfico a todos los puertos.

#### Checksum Offloading en Entornos Virtuales
* **Desafío:** Suricata descartaba paquetes debido a sumas de verificación (checksums) inválidas, generadas por la delegación de cómputo de los drivers VirtIO al hipervisor.
* **Solución:** Modificación del archivo de configuración de Suricata para deshabilitar la validación estricta: `checksum-validation: no`.

#### Persistencia mediante SaltStack
* **Desafío:** Los cambios manuales en configuraciones críticas se perdían tras reiniciar servicios debido a la orquestación de Security Onion.
* **Solución:** Edición directa del archivo maestro **Pillar (.sls)** en SaltStack para asegurar la persistencia del mapeo de interfaces físicas (ens19) y variables del motor de detección.

---
