<div align="center">

# AIRUH 愛
### Adaptive Interoperability Resilient Unit for Health

**Un agente autónomo de interoperabilidad clínica universal**  
*An autonomous agent for universal clinical interoperability*

[![License: Apache 2.0](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)
[![Status: Research](https://img.shields.io/badge/Status-Research%20%7C%20In%20Progress-yellow.svg)]()
[![FHIR R4](https://img.shields.io/badge/FHIR-R4-orange.svg)](https://hl7.org/fhir/R4/)
[![Python](https://img.shields.io/badge/Python-3.11%2B-blue.svg)](https://python.org)
[![arXiv](https://img.shields.io/badge/arXiv-coming%20soon-red.svg)]()

[🌐 airuh.org](https://airuh.org) · [📄 Paper (coming soon)]() · [🏥 airuh.health](https://airuh.health) · [🐦 @jaiderreyes](https://linkedin.com/in/jaiderreyes)

</div>

---

## ¿Qué es AIRUH? / What is AIRUH?

> *"Interoperar significa que el conocimiento clínico acompañe al paciente — y no quede atrapado en una institución."*  
> — Jaider Reyes Herazo

**AIRUH** (Adaptive Interoperability Resilient Unit for Health) es un agente autónomo de IA diseñado para conectar historias clínicas fragmentadas desde cualquier fuente de datos, en cualquier infraestructura, bajo cualquier marco normativo — sin requerir que la institución de salud ya tenga FHIR implementado.

**AIRUH** is an autonomous AI agent designed to connect fragmented clinical records from any data source, in any infrastructure, under any regulatory framework — without requiring the health institution to already have FHIR implemented.

El nombre incorpora el carácter japonés **愛** (ai = amor, cuidado), reflejando el principio fundacional: *la interoperabilidad clínica es, en última instancia, un acto de cuidado hacia el paciente.*

---

## El problema / The problem

Los sistemas de salud globales — especialmente en entornos de bajos recursos (LMIC) — enfrentan tres barreras simultáneas que ningún sistema existente resuelve de forma integrada:

| Barrera | Estado actual | AIRUH |
|---|---|---|
| Fuentes heterogéneas (Excel, HL7v2, papel) | Requieren FHIR nativo | Adaptador universal |
| Conectividad intermitente (3G, sin red) | Requieren conexión estable | Modo offline-first |
| Compliance multi-jurisdiccional | Hardcodeado por país | Motor de normas dinámico |

---

## Arquitectura / Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     AIRUH Agent Core                        │
│                                                             │
│  ┌─────────────┐  ┌──────────────┐  ┌───────────────────┐  │
│  │  Universal  │  │   Semantic   │  │   Clinical LLM    │  │
│  │   Adapter   │→ │  Normalizer  │→ │     Engine        │  │
│  │             │  │              │  │  (LangGraph+MCTS) │  │
│  │ CSV·HL7v2   │  │ SNOMED·LOINC │  │                   │  │
│  │ FHIR·JSON   │  │ CIE-10·RDA   │  │  Tool-use·RAG     │  │
│  └─────────────┘  └──────────────┘  └───────────────────┘  │
│                                              │               │
│  ┌───────────────────────┐  ┌───────────────▼─────────────┐ │
│  │   Compliance Engine   │  │    Resilient Deploy Layer   │ │
│  │                       │  │                             │ │
│  │  🇨🇴 Res 1888/2025    │  │  Edge · On-premise · Cloud  │ │
│  │  🇺🇸 HIPAA            │  │  Offline-first · 3G-ready   │ │
│  │  🇪🇺 GDPR             │  │  Docker · K8s · llama.cpp   │ │
│  └───────────────────────┘  └─────────────────────────────┘ │
└─────────────────────────────────────────────────────────────┘
         │                              │
         ▼                              ▼
  HAPI FHIR R4 Server          Clínico / Paciente / IPS
  (Keycloak · SMART on FHIR)   (cualquier dispositivo)
```

---

## Módulos / Modules

### 1. Universal Adapter
Convierte cualquier fuente de datos clínicos a FHIR R4 Bundle.

```python
from airuh.adapter import UniversalAdapter

adapter = UniversalAdapter()

# Desde CSV de IPS básica
bundle = adapter.from_csv("pacientes.csv", profile="colombia-r1888")

# Desde HL7 v2 legacy
bundle = adapter.from_hl7v2(hl7_message)

# Desde formulario JSON libre
bundle = adapter.from_json(clinical_form)
```

### 2. Semantic Normalizer
Mapea terminología libre a vocabularios clínicos estándar.

```python
from airuh.normalizer import SemanticNormalizer

norm = SemanticNormalizer(terminologies=["SNOMED-CT", "LOINC", "CIE-10"])
normalized = norm.normalize("dolor de cabeza")
# → SNOMED: 25064002 | "Headache (finding)"
```

### 3. Clinical LLM Engine
Motor de razonamiento clínico con tool-use sobre FHIR y RAG sobre guías clínicas.

```python
from airuh.agent import ClinicalAgent

agent = ClinicalAgent(
    llm="claude-sonnet-4-6",       # Claude API
    fallback_llm="qwen3:9b",       # Local / offline (llama.cpp)
    fhir_server="https://your-hapi-server/fhir",
    audit_log=True
)

response = agent.query(
    patient_id="COL-12345",
    question="¿Cuál es el estado actual de medicamentos del paciente?",
    user_role="physician"
)
```

### 4. Compliance Engine
Perfiles normativos intercambiables sin cambiar el código.

```python
from airuh.compliance import ComplianceEngine

# Colombia
engine = ComplianceEngine.load("colombia-res-1888-2025")

# HIPAA
engine = ComplianceEngine.load("hipaa-2026")

# GDPR
engine = ComplianceEngine.load("gdpr-eu")
```

### 5. Resilient Deploy Layer
Modo offline-first con sincronización automática al recuperar red.

```python
from airuh.resilience import ResilientFHIRClient

client = ResilientFHIRClient(
    primary="https://your-hapi-server/fhir",
    local_cache=True,          # SQLite local como FHIR store mínimo
    sync_on_reconnect=True,    # Sincroniza al volver la red
    min_bandwidth_kb=10        # Funciona desde 10 KB/s (3G básico)
)
```

---

## Estado del proyecto / Project Status

| Fase | Descripción | Estado |
|---|---|---|
| **S1-S4** | Estado del arte + gap statement | 🔄 En progreso |
| **S5** | Arquitectura técnica v0.1 | ⏳ Jun 22, 2026 |
| **S6** | Universal Adapter prototipo | ⏳ Jun 29, 2026 |
| **S7** | Motor LLM + FHIR tools | ⏳ Jul 6, 2026 |
| **S8** | Compliance Engine + offline | ⏳ Jul 13, 2026 |
| **S9** | Validación IPS Sucre, Colombia | ⏳ Jul 20, 2026 |
| **S10** | Análisis resultados + v0.2 | ⏳ Jul 27, 2026 |
| **S11** | Paper v1 + landing page | ⏳ Ago 3, 2026 |
| **S12** | Preprint arXiv + lanzamiento | ⏳ Ago 10, 2026 |

---

## Stack tecnológico / Tech stack

| Capa | Tecnología |
|---|---|
| LLM principal | Claude Sonnet (Anthropic API) |
| LLM local/offline | Qwen3 9B vía llama.cpp |
| Orquestación agente | LangGraph |
| FHIR server | HAPI FHIR R4 |
| Autenticación | Keycloak + SMART on FHIR (OAuth2) |
| API backend | FastAPI (Python 3.11+) |
| Deploy | Docker · Kubernetes · edge |
| Base local offline | SQLite |
| Terminologías | SNOMED CT · LOINC · CIE-10 · RDA Col |

---

## Diferenciación / What makes AIRUH different

| Característica | ClinicalAgents (MIT/HK 2026) | MCP-FHIR | **AIRUH** |
|---|---|---|---|
| Razonamiento diagnóstico | ✓ MCTS | — | ✓ incorporado |
| Fuentes heterogéneas | ✗ solo texto | ✗ solo FHIR | ✅ cualquier fuente |
| Modo offline | ✗ | ✗ | ✅ offline-first |
| Compliance dinámico | ✗ | ✗ | ✅ por jurisdicción |
| Validado en LMIC real | ✗ solo benchmark | ✗ | ✅ IPS Sucre, Colombia |
| Human-in-the-loop | implícito | — | ✅ estructural |

---

## Instalación rápida / Quick start

```bash
# Clonar el repositorio
git clone https://github.com/jaiderreyes/airuh-health.git
cd airuh-health

# Copiar variables de entorno
cp .env.example .env
# Editar .env con tus credenciales FHIR, Anthropic API key, etc.

# Levantar con Docker Compose
docker compose up -d

# Verificar que el agente responde
curl http://localhost:8000/health
```

---

## Roadmap de investigación / Research roadmap

Este repositorio acompaña el paper:

> **AIRUH: An Adaptive Interoperability Resilient Agent for Universal Clinical Context Orchestration in Resource-Constrained Health Settings**  
> Jaider Enrique Reyes Herazo  
> UAJS · CECAR · Universidad del Norte  
> *Submitted to npj Digital Medicine (Nature) — 2026*

La investigación sigue metodología PRISMA-ScR para el estado del arte y validación empírica en IPS de nivel 1-2 en el Caribe colombiano.

---

## Sitios piloto / Pilot sites

¿Tienes una IPS, clínica o sistema de salud y quieres ser sitio piloto de AIRUH?

📧 Contacto: [jaiderreyes.com](https://jaiderreyes.com)  
🌐 Proyecto: [airuh.org](https://airuh.org)  
💼 LinkedIn: [jaiderreyes](https://linkedin.com/in/jaiderreyes)

---

## Autor / Author

**Jaider Enrique Reyes Herazo**  
Ingeniero de Sistemas · MSc Gestión de la Innovación · Candidato Doctoral Estudios Organizacionales

- Coordinador de Emprendimiento — UAJS, Sincelejo, Colombia
- Docente Ingeniería — CECAR · UNISUCRE · UPB Medellín
- Investigador en interoperabilidad clínica y transformación digital en salud

---

## Citación / Citation

```bibtex
@software{reyes2026airuh,
  author    = {Reyes Herazo, Jaider Enrique},
  title     = {AIRUH: Adaptive Interoperability Resilient Unit for Health},
  year      = {2026},
  url       = {https://github.com/jaiderreyes/airuh-health},
  note      = {Research prototype — Under active development}
}
```

---

## Licencia / License

```
Copyright 2026 Jaider Enrique Reyes Herazo

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0
```

---

<div align="center">

**AIRUH** — *Desde Sincelejo para el mundo.*  
*From Sincelejo to the world.*

愛 · Cuidando la historia clínica de cada paciente,  
desde cualquier fuente, en cualquier contexto, bajo cualquier norma.

</div>
