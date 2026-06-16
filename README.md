# Alexia (A Lex IA) ⚖️🤖

Alexia es una cáscara de arquitectura abierta para la implementación de una IA Legal Corporativa, privada y auto-alojada (*Self-Hosted*). Está diseñada específicamente para el fuero penal argentino y la gestión local de expedientes judiciales.

El sistema prioriza la **soberanía absoluta de la información**. Al ejecutar modelos de lenguaje locales desvinculados de la nube, se garantiza el secreto profesional, la privacidad de los clientes y el control total de los datos sensibles del estudio.

## 🧠 Motor de Inferencia (Laboratorio)

Para el entorno de desarrollo y pruebas, el sistema está dimensionado y optimizado para correr el siguiente modelo base:
* **Modelo:** `deepseek-r1:14b` (Modelo de razonamiento destilado, ideal para lógica jurídica compleja y análisis de declaraciones).
* **Entorno:** Desplegado de forma nativa mediante **Ollama** dentro de una máquina virtual aislada (Debian Linux bajo KVM/QEMU) conectada a un puente de red local (`br-labLAN`).

---

## 🏗️ Estructura de Directorios Operativa

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
