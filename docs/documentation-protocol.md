# Protocolo de documentación

Este proyecto se desarrolló bajo el protocolo de documentación propio del autor, replicado cross-proyecto en múltiples proyectos del ecosistema. **La documentación es activo de primera clase, no afterthought.**

> Este case-study es **anonimizado**. La identidad del cliente, sector específico, ubicaciones geográficas, nombres comerciales del stack proprietario del cliente (plataformas de servicios sobre las que opera), nombres de empresas asociadas downstream, identificadores de BD, volúmenes absolutos del negocio, terminología regulatoria sectorial específica y nombres de personas internas se omiten. El protocolo descrito es el mismo aplicado al proyecto real entregado bajo acuerdo de confidencialidad.

---

## 5 capas documentales

### 1. arc42 — Marco de arquitectura del software

Marco estandarizado de 12 secciones (introducción + objetivos · restricciones · contexto · estrategia de solución · vista de bloques · vista de runtime · vista de despliegue · conceptos cross-cutting · decisiones arquitectónicas · requisitos de calidad · riesgos · glosario).

**En este repo (versión pública anonimizada):**
- Resumen sistémico del producto en el README (secciones *Sobre este case-study* + *El problema*).
- Estrategia de solución en *La solución arquitectónica* (2 subsistemas aislados).
- Vista de runtime detallada en *Workflows principales* (3 sequence diagrams: LLM agent con tool dispatch · anti-hallucination con tool dedicada · dashboard query con masking).
- Vista de despliegue resumida en *Estado y madurez técnica* + *Escala y operación*.
- Conceptos cross-cutting: PII masking · audit log append-only · autorización app-level · aislamiento entre subsistemas.
- Resto vive en el entregable original bajo NDA.

### 2. C4 — Modelo visual de arquitectura

Notación Simon Brown: Context · Container · Component · Code. Diagramas embebidos como **Mermaid** para render nativo en GitHub.

**En este repo:**
- [`assets/diagrams/c4-context.mmd`](../assets/diagrams/c4-context.mmd) — diagrama Context (3 actores + 2 subsistemas + 5 sistemas externos).
- [`assets/diagrams/c4-container.mmd`](../assets/diagrams/c4-container.mmd) — diagrama Container (2 subsistemas aislados + warehouse compartido + ETL upstream).
- Component + Code permanecen en repo privado por superficie técnica sensible (lógica específica de los 13 tools tipadas, system prompt versionado, mapeo concreto del star schema cliente-específico).

### 3. ADR — Architecture Decision Records

Cada decisión estructural del proyecto real se documentó como ADR versionada con plantilla fija:

```
Contexto      → qué problema / qué tensión
Decisión      → qué se eligió
Alternativas  → qué más se evaluó y por qué se descartó
Consecuencias → trade-offs aceptados (positivos y negativos)
Plan rollout  → cómo se materializa
Plan rollback → cómo se revierte si rompe algo
```

**11 ADRs versionadas** en el entregable original (catálogo formal en `docs/adr/index.md`). Permanecen bajo NDA. Este case-study cita en el README **5 ADRs representativas** anonimizadas (typed tools cerrado vs text-to-SQL · aislamiento absoluto entre subsistemas · autorización app-level vs RLS · anti-hallucination con tool dedicada + DESAMBIGUACIÓN · regresión SQL semántica en CI).

### 4. Runbooks operativos

Procedimientos entregados al cliente bajo NDA:

- **Deploy runbook** por subsistema (stacks Docker independientes · sin tocar el otro subsistema).
- **LLM cost monitoring runbook** (revisión mensual de tokens + coste agregado + alerta si supera umbral).
- **LLM model rotation runbook** (cambiar tier default sin downtime · A/B testing por env var).
- **Audit query runbook** (reconstruir timeline de queries por usuario o por tool desde `bot_interactions` / `query_log`).
- **ETL freshness alert runbook** (qué hacer si `data_freshness > umbral`).
- **Schema drift runbook** (cuando schema upstream cambia · regenerar dump · re-correr tests · actualizar vistas).
- **Tool addition runbook** (procedimiento para añadir tool nueva al catálogo cerrado: schema → SQL → tests → deploy).

Cada uno con cabecera fija: disparadores · precondiciones · severidad · owner · pasos numerados · verificación final.

### 5. Postmortems formales

Cada incidente productivo cierra con postmortem versionado: cronología · causa raíz (5 whys) · acción correctiva (que escala a ADR si aplica) · métrica observable para prevenir recurrencia. Los aprendizajes transferibles (no específicos del cliente) alimentan el `engineering-playbook` cross-proyecto del autor.

**Patrones de retos técnicos resueltos** documentados como case-study generalizable en la sección "Retos técnicos resueltos" del README (6 retos con estructura Síntoma / Causa raíz / Fix / Lección).

---

## Disciplina cross-proyecto

- **Engineering-playbook propio** con 60+ archivos doctrinales cross-proyecto, evolucionados iterativamente desde incidentes reales (no copy-paste de buenas prácticas genéricas).
- **`verify-findings` adversarial**: cualquier auditoría / hallazgo pasa por un segundo agente independiente antes de cerrar. Ratio de ruido objetivo < 15 %.
- **Convenciones**: español como idioma de trabajo · sin emojis decorativos · tono operativo y directo · "lo que no aporta evidencia, fuera".
- **Stage-Gate Pre-dev/Post-dev protocol** con auditoría adversarial antes de merge.
- **Tests de regresión SQL semántica** contra schema dumped diariamente en CI.
- **Aislamiento absoluto** entre subsistemas del mismo cliente cuando tienen responsabilidades distintas.

---

*Detalles operativos completos del playbook y del entregable original bajo acuerdo de confidencialidad.*
