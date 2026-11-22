# Despliegue de un Clúster Multi-Nodo Apache Hadoop con Docker, Auditoría Física en HDFS y Simulación de Tolerancia a Fallos

Laboratorio práctico de Infraestructura como Código (IaC) desplegado con **Docker Compose** para auditar el almacenamiento distribuido en **HDFS** (partición de bloques y factor de replicación), validar la resiliencia ante caídas de nodos en caliente y ejecutar cómputo distribuido **MapReduce sobre YARN**.

---

## Autoría y Datos Académicos

* **Autor/a:** Zanoni Garcia
* **Centro Educativo:** Campus Cámara de Comercio de Sevilla
* **Ciclo Formativo:** Curso de Especialización en Inteligencia Artificial y Big Data (IABD)
* **Módulo Profesional:** 5074 / 5075 – Sistemas de Big Data / Infraestructuras Big Data
* **Resultado de Aprendizaje:** RA 2 – Gestión de sistemas de almacenamiento y procesamiento distribuido
* **Fecha:** 22/11/2026

---

## Ficha Resumen de la Arquitectura Desplegada

El entorno ejecuta **6 contenedores** basados en la imagen oficial `apache/hadoop:3` interconectados mediante una red puente aislada (`hadoop-net`), separando el plano de almacenamiento (HDFS) y el de gestión de recursos/cómputo (YARN):

| Servicio / Contenedor | Rol en el Clúster | Capa | Puertos Expuestos |
| :--- | :--- | :--- | :--- |
| `namenode` | Nodo maestro de metadatos y gestión del espacio de nombres | HDFS | `9870:9870` (Web UI) |
| `datanode1` | Nodo trabajador de almacenamiento de bloques físicos | HDFS | — (Interno `hadoop-net`) |
| `datanode2` | Nodo trabajador de almacenamiento de bloques físicos | HDFS | — (Interno `hadoop-net`) |
| `datanode3` | Nodo trabajador de almacenamiento de bloques físicos | HDFS | — (Interno `hadoop-net`) |
| `resourcemanager` | Planificador maestro global de recursos de cómputo | YARN | `8088:8088` (Web UI) |
| `nodemanager` | Agente trabajador ejecutor de contenedores de tareas | YARN | — (Interno `hadoop-net`) |

### Parámetros clave de configuración (`docker/config`)

* **Sistema de ficheros por defecto (`fs.defaultFS`):** `hdfs://namenode:8020`
* **Tamaño de bloque lógico (`dfs.blocksize`):** `67108864` bytes (**64 MiB**)
* **Factor de replicación (`dfs.replication`):** `2` ($R = 2$)
* **Recursos asignados al NodeManager:** `2048 MB` de RAM y `2` vCores

---

## Estructura del Repositorio

```text
IABD-5075-LabHadoop-Apellido1-Apellido2/
├── .gitignore
├── README.md
├── docker/
│   ├── config
│   └── docker-compose.yml
├── evidencias/
│   ├── 01_namenode_ui_9870.png
│   ├── 02_hdfs_fsck_bloques.png
│   ├── 03_simulacion_fallo_datanode2.png
│   └── 04_yarn_wordcount_8088.png
└── informe/
    └── Apellido1_Apellido2_InformeLabHadoop.pdf
```

---

## Requisitos Previos

* **Docker Desktop** o **Docker Engine** con **Docker Compose v2** operativo.
* Al menos **6 GB de memoria RAM** libre en el equipo anfitrión.
* **Git** instalado para el control de versiones.

---

## Instrucciones de Arranque y Operación

### 1. Clonar el repositorio

```bash
git clone [https://github.com/tu-usuario/IABD-5075-LabHadoop-Apellido1-Apellido2.git](https://github.com/tu-usuario/IABD-5075-LabHadoop-Apellido1-Apellido2.git)
cd IABD-5075-LabHadoop-Apellido1-Apellido2
```

### 2. Levantar el clúster Hadoop

```bash
cd docker
docker compose up -d
docker compose ps
```

### 3. Acceso a las interfaces web de monitorización

* **HDFS NameNode UI:** [http://localhost:9870](http://localhost:9870) (estado de los DataNodes y explorador del sistema de ficheros).
* **YARN ResourceManager UI:** [http://localhost:8088](http://localhost:8088) (monitorización de aplicaciones MapReduce).

### 4. Acceso a la terminal del nodo maestro

```bash
docker exec -it namenode bash
```

### 5. Detener el clúster y liberar recursos

```bash
docker compose down -v
```

---

## Documentación Técnica

El análisis detallado de cada fase, las salidas de terminal auditadas (`hdfs dfsadmin -report`, `hdfs fsck`) y las respuestas argumentadas a las cuestiones técnicas se encuentran disponibles en el informe final: [`informe/Apellido1_Apellido2_InformeLabHadoop.pdf`](informe/Apellido1_Apellido2_InformeLabHadoop.pdf).
