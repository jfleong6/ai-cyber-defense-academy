# Reflexión — Módulo 01: Propósito, ética y mentalidad del defensor

**Nombre:** Jhon Fredy León García · **Fecha:** 03/11/2026

## 1. ¿Qué aprendí?

1. **El porqué va primero.** Con el Golden Circle (WHY → HOW → WHAT) entendí que la herramienta (Wazuh, Wireshark, ATT&CK…) es lo último. Primero está a quién protejo y por qué, luego cómo me anticipo y al final qué uso.
2. **El cibercrimen se volvió industria.** FraudGPT, WormGPT o la clonación de voz se venden como suscripción, con soporte y marketing. La barrera de entrada cayó: ya no hace falta saber programar para atacar. Y como toda industria, **recluta**.
3. **El tiempo se comprimió.** La ventana hasta la explotación de una vulnerabilidad crítica pasó de días a **24–48 horas** (FortiGuard). Con IA, el atacante ya no se detiene cuando falla: **replanifica**. Defender solo de forma reactiva ya no alcanza.
4. **El modelo también es un activo que se ataca.** Inyección indirecta, envenenamiento, evasión, inversión, robo del modelo, instrucciones ocultas con emojis. MITRE ATLAS es el mapa para eso.
5. **Gravedad ≠ prioridad.** Una CVE con CVSS alto no siempre es lo más urgente. El riesgo depende de exposición, criticidad, impacto y probabilidad.
6. **Hecho ≠ hipótesis** (Detective Digital, LAB 01). Analizar es separar lo que sé, lo que supongo y qué evidencia me falta antes de decidir.
7. **Poder no es deber.** Lo que separa a un pentester de un delincuente no es la técnica, sino la **autorización escrita, el alcance y la evidencia** (Ley 1273 de 2009, art. 269A).

## 2. ¿Qué me impactó?

- Que un agente de IA, intentando cumplir una tarea legítima (investigar un gasto público), **tomara acciones no autorizadas** y terminara dentro de sistemas de un gobierno. El adversario no siempre es una persona con malas intenciones; también puede ser un sistema con demasiada autonomía y sin límites.
- El caso **BragJack**: una extensión "normal" de navegador pudo dar órdenes completas al asistente de IA del navegador (*Prompt-Forcing*). Las herramientas que más confianza nos dan pueden ser la puerta de entrada.
- Lo que pasó en la segunda sesión, cuando el profe empezó a llamarnos **"Pentester IA"**, y su pregunta sobre lo que ocurrió durante la clase: **"¿Usaron IA?"**. Hoy la pregunta ya no es si el atacante sabe programar, sino si tiene un agente que lo haga por él. Y el defensor tiene que hacerse la misma pregunta.

## 3. ¿Cómo lo aplico?

- **Como desarrollador:** tratar todo contenido externo (PDF, correo, web, respuestas de APIs y herramientas) como **no confiable** cuando lo consume un LLM o un agente. Una fuente de datos no debe convertirse en fuente de instrucciones.
- **Con mis herramientas:** revisar qué extensiones y agentes tengo instalados, qué permisos tienen y si los necesito de verdad (mínimo privilegio).
- **Antes de cualquier práctica:** autorización, alcance, evidencia.
- **Con mi IA de uso diario:** configurarla con el Prompt Maestro del curso para que me haga preguntas en vez de darme todo resuelto.

## 4. Preguntas que me quedan

1. ¿Cómo se valida en la práctica que un guardrail de IA funciona y no solo que "está configurado"?
2. ¿Dónde está la frontera entre un agente que "investiga" y uno que "intrusiona"? ¿Quién responde legalmente?
3. Si las herramientas que más uso (extensiones, asistentes de IA en el navegador o en el editor de código) pueden ser la puerta de entrada, ¿cómo audito mi propio entorno de desarrollo sin volverme paranoico ni dejar de usarlas?

## 5. Nivel actual (escala del curso)

**RECONOCE** → aplica → analiza → transfiere.
Meta para la sección 3: llegar a **APLICA** construyendo mi primer Attack Flow.
