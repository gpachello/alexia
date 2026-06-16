# Alexia (A Lex IA) ⚖️

Alexia es una cáscara de arquitectura abierta para la implementación de una IA Legal Corporativa, privada y auto-alojada (*Self-Hosted*). Está diseñada específicamente para el fuero penal argentino y la gestión local de expedientes judiciales.

El sistema prioriza la **soberanía absoluta de la información**. Al ejecutar modelos de lenguaje locales desvinculados de la nube, se garantiza el secreto profesional, la privacidad de los clientes y el control total de los datos sensibles del estudio.

## ⚙️ Motor de Inferencia (Laboratorio)

Para el entorno de desarrollo y pruebas, el sistema está dimensionado y optimizado para correr el siguiente modelo base:
* **Modelo:** `deepseek-r1:14b` (Modelo de razonamiento destilado, ideal para lógica jurídica compleja y análisis de declaraciones).
* **Entorno:** Desplegado de forma nativa mediante **Ollama** dentro de una máquina virtual aislada (Debian Linux bajo KVM/QEMU) conectada a un puente de red local (`br-labLAN`).

---
## 💻 Dimensionamiento de la Máquina Virtual

El entorno virtualizado (Debian Linux bajo KVM/QEMU) está calculado para operar de forma holgada sin sobredimensionar el hardware del host:

* **Memoria RAM (16 GB):**
  * Espacio óptimo y crítico. El modelo `deepseek-r1:14b` (en su cuantización por defecto de 4 bits) pesa aproximadamente 9 GB en disco. Al cargarse en memoria mediante la directiva `OLLAMA_KEEP_ALIVE=-1`, consume cerca de 10-11 GB netos. Los ~5 GB restantes quedan completamente libres para el sistema operativo Debian, la base de datos PostgreSQL/pgvector y la ejecución de los scripts indexadores en Python.
* **Almacenamiento (50 GB - Espacio Asignado):**
  * ~10 GB para el sistema operativo base Debian limpio y dependencias esenciales.
  * ~10 GB para los archivos binarios y las capas del modelo `deepseek-r1:14b-alexia`.
  * ~30 GB disponibles netos para el crecimiento del repositorio local (`storage/`), permitiendo albergar miles de páginas de expedientes escaneados, texto plano indexado, la base de datos relacional y digesto de normas sin riesgo de saturación de disco (*Disk Full*).
## 🏗️ Estructura de Directorios Operativa

--- 

El diseño del sistema operativo separa de forma estricta la lógica de los scripts del almacenamiento dinámico de datos. Toda la infraestructura de Alexia se consolida bajo la jerarquía de servicios de `/var/lib/ollama/` para evitar conflictos de permisos y facilitar las políticas de backup:

```text
/var/lib/ollama/
├── .ollama/                 # Core interno de Ollama y blobs de modelos (Fijo de sistema)
└── alexia/                  # Espacio de nombres e identidad del proyecto
    ├── config/              # Modelfiles personalizados y overrides de Systemd
    ├── scripts/             # Automatizaciones y pipelines de indexación (Python/Bash)
    └── storage/             # Repositorio local de documentos (Protegido en .gitignore)
        ├── causas/          # Subcarpetas indexadas por ID numérico de expediente (PDFs)
        └── normas/          # Modelos estáticos de referencia (Código Penal, CPP, Jurisprudencia)
```
---

## 🛠️ Estado Actual del Proyecto y Roadmap

El proyecto se encuentra en fase activa de **Desarrollo Inicial e Infraestructura**. Las bases del entorno de virtualización y el motor de inferencia local ya están consolidados.

Las próximas etapas planificadas para el desarrollo de **Alexia** incluyen:

- [x] **Fase 1:** Diseño de arquitectura base, permisos de sistema de archivos (`SGID`) y despliegue del modelo `deepseek-r1:14b` en entorno Debian virtualizado.
- [ ] **Fase 2 (En Desarrollo):** Diseño del esquema de base de datos relacional y relacional-vectorial utilizando **PostgreSQL** y la extensión `pgvector` para el almacenamiento de metadatos de las causas y *embeddings*.
- [ ] **Fase 3:** Desarrollo del pipeline de ingesta de datos en Python (`scripts/pipeline_indexer.py`) para el procesamiento, segmentación (*chunking*) y vectorización automática de documentos PDF depositados en `storage/causas/` y `storage/normas/`.
- [ ] **Fase 4:** Implementación de la lógica de **RAG (Retrieval-Augmented Generation)** para que el modelo responda consultas legales basándose estrictamente en el contexto documental local inyectado.

---
