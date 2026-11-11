# Aporte proactivo 01 · Kali365

**Primer aporte proactivo** de la AI Cyber Defense Academy: un análisis defensivo del kit de phishing como servicio **Kali365**, que roba sesiones de Microsoft 365 sin necesitar la contraseña. El caso funciona como laboratorio sobre los **tokens delegados**, la llave que comparten las personas y sus asistentes de IA.

Sale del tablero de la clase del 05/11/2026: «Defensa proactiva → Análisis defensivo».
- **Laboratorio:** Kali365.
- **Público:** líderes de seguridad, empresarios y usuarios.
- **Fases:** prevenir, detectar, contener y recuperar.
- **Insights:** ecosistema del cibercrimen, dónde reportar y marco legal.

## Qué hay aquí

| Archivo | Para quién | Qué contiene |
|---|---|---|
| [`reporte-tecnico.md`](reporte-tecnico.md) | Analistas SOC, equipos de identidad y responsables de IA | Golden Circle; cadena de ataque mapeada a MITRE ATT&CK v20.1 y MITRE ATLAS 2026.10; Attack Flow con las ramas código de dispositivo y AiTM; matriz prevenir/detectar/contener/recuperar con responsable, plazo y evidencia; tres puntos de corte; análisis crítico de cada medida del artículo; ejercicio de mesa de 60 minutos; versiones técnica y ejecutiva; insights. |
| [`reporte-sencillo.md`](reporte-sencillo.md) | Empresarios, gerentes y todo el personal | Lo mismo, sin jerga: analogías, cuatro fases con quién y cuándo, insights y un glosario corto. |

## Cómo está hecho

- **Fuentes principales:**
  - Arctic Wolf Labs, «Token Bingo» (24/04/2026).
  - La guía de phishing de CISA, NSA, FBI y MS-ISAC (octubre de 2023).
- **Fuentes de apoyo:** el aviso del FBI IC3 (21/05/2026), MITRE ATT&CK S9044, Microsoft Learn y fuentes oficiales colombianas para los canales de denuncia y las leyes.
- **Etiquetas:** **[HECHO]**, **[HIPÓTESIS]** y **[SUPOSICIÓN]**. Cada control dice si viene del artículo, de otra fuente o si es una propuesta mía.
- **IDs de MITRE:** solo los que verifiqué en los sitios oficiales el 05/11/2026.
- **Indicadores:** desactivados, por ejemplo `kali365[.]xyz`.
- **Enfoque:** solo defensivo. No hay código ofensivo ni pasos reproducibles, y no se ejecutó ningún ataque.

> «No preguntes solamente si ocurrió. Pregunta si resistiríamos si ocurriera.»

*Jhon Fredy León García · 05/11/2026 · No es asesoría legal.*
