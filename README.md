# 🛡️ ContainerShield

### Dockerfile Hardening Analyzer

> **Detect. Understand. Harden. Deploy.**

ContainerShield es una plataforma de análisis y hardening de Dockerfiles que identifica configuraciones inseguras, explica los riesgos encontrados y propone una versión corregida del archivo.

Su objetivo es llevar la seguridad **antes del despliegue**, ayudando a desarrolladores y equipos DevOps a detectar problemas de configuración desde las primeras etapas del desarrollo.

---

<p align="center">

**🔍 Analyze**  →  **🧠 Understand**  →  **🛠️ Harden**  →  **🚀 Deploy**

</p>

---

## 🚨 El problema

Los contenedores simplifican enormemente el desarrollo y despliegue de aplicaciones, pero una configuración incorrecta puede aumentar considerablemente la superficie de ataque.

Un Dockerfile aparentemente funcional puede contener problemas como:

- 🔴 Ejecución como `root`
- 🔴 Uso de imágenes base no confiables o impredecibles
- 🔴 Exposición innecesaria de puertos
- 🔴 Montajes peligrosos del host
- 🔴 Uso de privilegios excesivos
- 🔴 Credenciales o secretos incluidos en el archivo
- 🟠 Ausencia de límites de recursos
- 🟡 Instalación de paquetes innecesarios
- 🟡 Uso de instrucciones Docker poco recomendadas

El problema no siempre es que el desarrollador desconozca la seguridad.

El problema es que **la seguridad frecuentemente se revisa demasiado tarde**.

---

# 💡 La solución

## ContainerShield

ContainerShield analiza un Dockerfile antes de que llegue a producción.

El sistema transforma:

```text
Dockerfile
     │
     ▼
┌──────────────────┐
│    ContainerShield│
└────────┬─────────┘
         │
         ▼
   Análisis de seguridad
         │
    ┌────┴────┐
    ▼         ▼
 Hallazgos  Recomendaciones
    │         │
    └────┬────┘
         ▼
 Dockerfile endurecido
```

En lugar de simplemente indicar:

> ❌ "Existe una vulnerabilidad."

ContainerShield busca proporcionar:

> ⚠️ **Qué está mal**
> 🧠 **Por qué representa un riesgo**
> 🛠️ **Cómo solucionarlo**
> 📄 **Cómo quedaría el Dockerfile corregido**

---

# ⚡ Demo conceptual

### Antes

```dockerfile
FROM ubuntu:latest

USER root

ENV DB_PASSWORD=secret

EXPOSE 22
```

ContainerShield puede identificar:

| Finding               | Severidad |
| --------------------- | --------- |
| Uso de `latest`       | 🟠 MEDIUM |
| Ejecución como `root` | 🔴 HIGH   |
| Credencial expuesta   | 🔴 HIGH   |
| Exposición de SSH     | 🟠 MEDIUM |

---

### Después

```dockerfile
FROM ubuntu:22.04

RUN useradd -m appuser

USER appuser
```

La aplicación sigue siendo un contenedor.

La diferencia es que ahora parte de una configuración **más controlada y endurecida**.

---

# 🧠 Inteligencia de análisis

ContainerShield utiliza un agente basado en **Koog** junto con **Ollama** para complementar el análisis de seguridad.

```text
                  Dockerfile
                      │
                      ▼
             ┌─────────────────┐
             │  Javalin API    │
             └────────┬────────┘
                      │
                      ▼
             ┌─────────────────┐
             │   Koog Agent    │
             └────────┬────────┘
                      │
                      ▼
                Ollama Model
                      │
                      ▼
             ┌─────────────────┐
             │ Security Analysis│
             └────────┬────────┘
                      │
             ┌────────┴────────┐
             ▼                 ▼
        Findings          Recommendations
             │                 │
             └────────┬────────┘
                      ▼
               Hardened Output
```

La IA no sustituye las reglas de seguridad.

El objetivo es combinar:

**reglas determinísticas + análisis contextual + explicación inteligente.**

---

# 🎯 ¿Qué analiza?

ContainerShield está diseñado para detectar diferentes categorías de problemas de hardening.

### 🔐 Privilegios

Detecta configuraciones que pueden otorgar permisos innecesarios al contenedor.

Ejemplo:

```dockerfile
USER root
```

---

### 📦 Imágenes base

Identifica el uso de imágenes demasiado genéricas o etiquetas que pueden cambiar inesperadamente.

Ejemplo:

```dockerfile
FROM ubuntu:latest
```

Una alternativa más controlada:

```dockerfile
FROM ubuntu:22.04
```

---

### 🌐 Exposición de servicios

Analiza puertos declarados innecesariamente.

Ejemplo:

```dockerfile
EXPOSE 22
```

---

### 🔑 Secretos

Busca información sensible escrita directamente dentro del Dockerfile.

Ejemplo:

```dockerfile
ENV DB_PASSWORD=supersecret
```

---

### 💾 Montajes peligrosos

Identifica configuraciones que pueden proporcionar acceso innecesario al sistema anfitrión.

---

### ⚙️ Recursos

Detecta configuraciones donde pueden faltar mecanismos de control de recursos.

---

### 📋 Instrucciones Docker

Analiza patrones como:

```dockerfile
ADD
```

cuando una alternativa más específica puede ser:

```dockerfile
COPY
```

---

# 🎨 Dashboard

ContainerShield proporciona una interfaz web para analizar y consultar Dockerfiles.

```text
┌──────────────────────────────────────────────────────┐
│                    ContainerShield                   │
├──────────────────────────────────────────────────────┤
│                                                      │
│   Dockerfile Analysis                                │
│                                                      │
│   ┌──────────────────────────────────────────────┐   │
│   │ Upload Dockerfile                            │   │
│   │                                              │   │
│   │              📄                              │   │
│   │       Drag & Drop your file                  │   │
│   │                                              │   │
│   └──────────────────────────────────────────────┘   │
│                                                      │
│                 [ Analyze Dockerfile ]               │
│                                                      │
├──────────────────────────────────────────────────────┤
│                                                      │
│  🔴 HIGH      3                                      │
│  🟠 MEDIUM    4                                      │
│  🟡 LOW       2                                      │
│                                                      │
└──────────────────────────────────────────────────────┘
```

El usuario puede:

- 📤 Subir Dockerfiles
- 🔍 Ejecutar análisis
- 📊 Consultar hallazgos
- 🔴 Visualizar severidades
- 🧠 Consultar recomendaciones
- 📄 Revisar diferencias
- 🛠️ Obtener una versión corregida
- 🗂️ Consultar análisis anteriores

---

# 🏗️ Arquitectura

```text
                         ┌───────────────────┐
                         │      Usuario      │
                         └─────────┬─────────┘
                                   │
                                   ▼
                         ┌───────────────────┐
                         │    Vue 3 / UI     │
                         │     Vuetify       │
                         └─────────┬─────────┘
                                   │
                                   ▼
                         ┌───────────────────┐
                         │   Javalin API     │
                         │     Kotlin        │
                         └─────────┬─────────┘
                                   │
                 ┌─────────────────┼─────────────────┐
                 │                 │                 │
                 ▼                 ▼                 ▼
          ┌────────────┐    ┌────────────┐    ┌────────────┐
          │   Koog     │    │  Firebird  │    │   Docker   │
          │ AI Agent   │    │ Database   │    │ Environment│
          └─────┬──────┘    └────────────┘    └────────────┘
                │
                ▼
          ┌────────────┐
          │   Ollama   │
          │ AI Model   │
          └────────────┘
```

---

# 🧩 Technology Stack

| Componente       | Tecnología            |
| ---------------- | --------------------- |
| Frontend         | Vue 3                 |
| UI               | Vuetify               |
| Backend          | Kotlin                |
| API              | Javalin               |
| AI Agent         | Koog                  |
| AI Runtime       | Ollama                |
| Database         | Firebird              |
| Database Access  | Kotliquery / HikariCP |
| Containerization | Docker                |
| Orchestration    | Kubernetes            |
| Cloud            | AWS                   |
| Runtime          | Java 21               |

---

# 🔄 Flujo de análisis

```text
1. Usuario
      │
      ▼
2. Upload Dockerfile
      │
      ▼
3. Javalin recibe archivo
      │
      ▼
4. Análisis de instrucciones
      │
      ▼
5. Koog Agent
      │
      ▼
6. Ollama
      │
      ▼
7. Clasificación de findings
      │
      ├── 🔴 HIGH
      ├── 🟠 MEDIUM
      └── 🟡 LOW
      │
      ▼
8. Recomendaciones
      │
      ▼
9. Dockerfile corregido
      │
      ▼
10. Resultado almacenado
```

---

# 📊 Sistema de severidad

ContainerShield utiliza diferentes niveles para facilitar la interpretación de los resultados.

### 🔴 HIGH

Problemas que pueden representar un riesgo significativo y requieren atención prioritaria.

### 🟠 MEDIUM

Configuraciones que incrementan la superficie de ataque o pueden generar riesgos dependiendo del entorno.

### 🟡 LOW

Recomendaciones de hardening y buenas prácticas que ayudan a mejorar la configuración.

---

# 🗃️ Historial de análisis

Cada análisis puede almacenarse para permitir posteriormente:

```text
Dockerfile
     │
     ▼
 Analysis #001
     │
     ├── Findings
     ├── Severity
     ├── Recommendations
     └── Corrected Dockerfile
```

Esto permite construir posteriormente métricas como:

- Número de análisis
- Problemas detectados
- Problemas HIGH
- Evolución del hardening
- Historial por proyecto
- Tendencias de seguridad

---

# ☁️ Cloud Architecture

ContainerShield puede desplegarse sobre infraestructura cloud utilizando AWS.

Una arquitectura objetivo puede evolucionar hacia:

```text
                         Internet
                            │
                            ▼
                     ┌─────────────┐
                     │    AWS      │
                     └──────┬──────┘
                            │
                     ┌──────▼──────┐
                     │ Load Balancer│
                     └──────┬──────┘
                            │
                 ┌──────────┴──────────┐
                 ▼                     ▼
          ┌─────────────┐       ┌─────────────┐
          │ Application │       │ Application │
          │ Container   │       │ Container   │
          └──────┬──────┘       └──────┬──────┘
                 │                     │
                 └──────────┬──────────┘
                            ▼
                     ┌─────────────┐
                     │  Database   │
                     └─────────────┘
```

Esta arquitectura permite evolucionar desde un prototipo académico hacia una plataforma distribuida.

---

# 🚀 Roadmap

## ✅ Phase 1 — Foundation

- [x] Dockerfile upload
- [x] Web dashboard
- [x] Backend API
- [x] Database integration
- [x] Security findings
- [x] Severity classification
- [x] Recommendations
- [x] Corrected Dockerfile
- [x] Analysis history

---

## 🔄 Phase 2 — Intelligence

- [ ] Advanced AI reasoning
- [ ] Improved contextual analysis
- [ ] Automatic remediation
- [ ] Security explanation engine
- [ ] Custom security rules
- [ ] Analysis comparison

---

## ☸️ Phase 3 — Cloud Native

- [ ] Docker image analysis
- [ ] Kubernetes manifest analysis
- [ ] Helm analysis
- [ ] CI/CD integration
- [ ] Container registry integration
- [ ] SBOM generation

---

## 🌎 Phase 4 — Platform

- [ ] Multi-user organizations
- [ ] RBAC
- [ ] Enterprise policies
- [ ] Compliance reports
- [ ] Security dashboards
- [ ] API for external integrations
- [ ] SaaS deployment

---

# 💼 Potential Applications

ContainerShield puede utilizarse en diferentes escenarios:

### 👨‍💻 Desarrollo

Analizar Dockerfiles antes de realizar un build.

### 🔄 DevOps

Incorporar análisis dentro de pipelines CI/CD.

### ☸️ Kubernetes

Extender el análisis hacia manifests y configuraciones de despliegue.

### 🏢 Empresas

Definir políticas de seguridad para proyectos y equipos.

### 🎓 Educación

Utilizar la plataforma para enseñar principios de seguridad de contenedores.

### ☁️ Cloud

Integrar análisis dentro de flujos de despliegue cloud-native.

---

# 💰 Product Vision

ContainerShield comienza con una pregunta sencilla:

> **¿Qué problemas de seguridad tiene este Dockerfile?**

Pero puede evolucionar hacia una plataforma capaz de analizar diferentes componentes del ciclo de vida cloud-native:

```text
             SOURCE CODE
                  │
                  ▼
             DOCKERFILE
                  │
                  ▼
           CONTAINER IMAGE
                  │
                  ▼
             KUBERNETES
                  │
                  ▼
                CLOUD
                  │
                  ▼
        CONTINUOUS SECURITY
```

La visión es convertir el análisis de seguridad en una parte natural del proceso de desarrollo.

---

# 🔮 Future Vision

```text
                     ContainerShield
                           │
        ┌──────────────────┼──────────────────┐
        │                  │                  │
        ▼                  ▼                  ▼
   Containers         Kubernetes           Cloud
        │                  │                  │
        └──────────────────┼──────────────────┘
                           ▼
                  Security Intelligence
                           │
                           ▼
                  Automated Remediation
                           │
                           ▼
                 Secure Cloud Software
```

---

# 🧪 Example Finding

### Finding

```text
ID: CS-001

Severity: HIGH

Title:
Container running as root

Detected:
USER root
```

### Why?

Ejecutar procesos innecesariamente como `root` puede otorgar privilegios superiores a los requeridos por la aplicación.

### Recommendation

Crear un usuario dedicado para ejecutar la aplicación:

```dockerfile
RUN useradd -m appuser

USER appuser
```

### Result

```text
Before
──────
USER root

        ↓ ContainerShield

After
─────
USER appuser
```

---

# 🛠️ Installation

## Requirements

- Java 21
- Gradle
- Docker
- Ollama
- Firebird
- Node.js / npm
- Git

---

## Clone

```bash
git clone https://github.com/YOUR_USERNAME/containershield.git

cd containershield
```

---

## Configure Ollama

Instala Ollama y descarga el modelo utilizado por ContainerShield.

```bash
ollama pull <MODEL>
```

Inicia el servicio:

```bash
ollama serve
```

---

## Configure the database

Configura las variables correspondientes a Firebird:

```text
DB_URL=...
DB_USER=...
DB_PASSWORD=...
```

---

## Run the backend

```bash
./gradlew run
```

---

## Run the frontend

```bash
npm install
npm run dev
```

---

# 🐳 Docker

ContainerShield está diseñado para ejecutarse dentro de un entorno containerizado.

```bash
docker build -t containershield .

docker run -p 8080:8080 containershield
```

---

# 📁 Project Structure

```text
ContainerShield/
│
├── src/
│   ├── main/
│   │   ├── kotlin/
│   │   │   └── uttt/
│   │   │       └── edu/
│   │   │           └── net/
│   │   │
│   │   └── resources/
│   │
│   └── test/
│
├── public8/
│
├── vue/
│   ├── upload-docker/
│   ├── chatbot-enhance/
│   └── history-docker/
│
├── Dockerfile
├── build.gradle.kts
├── settings.gradle.kts
└── README.md
```

---

# 🔐 Security Philosophy

ContainerShield sigue una filosofía sencilla:

```text
                FIND
                 ↓
              EXPLAIN
                 ↓
              CORRECT
                 ↓
              VERIFY
                 ↓
               SHIP
```

La seguridad no debería aparecer únicamente después de un incidente.

**Debe formar parte del proceso de desarrollo.**

---

# 🌟 Why ContainerShield?

ContainerShield combina tres elementos:

### 🔍 Security

Detecta configuraciones inseguras.

### 🧠 Intelligence

Explica los problemas y ayuda a encontrar soluciones.

### 🛠️ Action

No se limita a reportar problemas: proporciona recomendaciones y una posible configuración corregida.

---

# 📈 From Scanner to Security Platform

```text
                 TODAY
                   │
                   ▼
          Dockerfile Analyzer
                   │
                   ▼
             AI Hardening
                   │
                   ▼
          Container Security
                   │
                   ▼
          Kubernetes Security
                   │
                   ▼
             Cloud Security
                   │
                   ▼
         Software Supply Chain
```

---

# 🛡️ ContainerShield

### **Secure before you ship.**

```text
┌────────────────────────────────────────────┐
│                                            │
│              🛡️ ContainerShield            │
│                                            │
│       Analyze. Harden. Deploy.             │
│                                            │
│    Security starts before production.      │
│                                            │
└────────────────────────────────────────────┘
```

---

## 📄 License

This project is currently under development.

License information will be added according to the project's distribution model.

---

## 👥 Project

**ContainerShield**

Dockerfile Hardening Analyzer

Built with:

`Kotlin` · `Javalin` · `Vue` · `Vuetify` · `Koog` · `Ollama` · `Firebird` · `Docker` · `Kubernetes` · `AWS`

> **Find the risk before the risk finds production.**
