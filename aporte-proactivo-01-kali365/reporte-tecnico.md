# Aporte proactivo 01 · Kali365: los tokens delegados como superficie de ataque (reporte técnico)

**Autor:** Jhon Fredy León García · AI Cyber Defense Academy (Campuslands × ETHOS IA Consultores)
**Fecha:** 05/11/2026 · **Clase:** M2S2, «Cadena de ataque y automatización ofensiva» · **Tablero:** «Defensa proactiva → Análisis defensivo»
**Público:** analistas SOC, equipos de identidad (Microsoft Entra ID) y responsables de IA. La versión para empresarios y usuarios está en [`reporte-sencillo.md`](reporte-sencillo.md).
**Fuentes principales:** Arctic Wolf Labs, «Token Bingo» (24/04/2026), y la guía de phishing de CISA, NSA, FBI y MS-ISAC (octubre de 2023). Las demás fuentes están al final.

> «No entrenamos hackers. Formamos personas capaces de proteger a otras personas.»
> «Una fuente de datos no debe convertirse automáticamente en una fuente de instrucciones.»

## 0. Cómo leer este reporte

| Regla | Cómo se aplica aquí |
|---|---|
| **Enfoque defensivo** | Del adversario se describe solo lo necesario para reconocerlo, detectarlo y romper la cadena. No hay código, consultas listas para copiar, plantillas de phishing ni pasos reproducibles. |
| **Etiquetas** | **[HECHO]**: lo confirma una fuente citada. **[HIPÓTESIS]**: explicación o escenario plausible que se puede comprobar o descartar. **[SUPOSICIÓN]**: premisa que asumo para el análisis, sin evidencia directa. |
| **Origen del control** | **Artículo**: lo recomienda Arctic Wolf en «Token Bingo». **Otra fuente**: lo recomienda el FBI, CISA o Microsoft. **Propuesto**: propuesta mía como analista. |
| **Indicadores** | Siempre desactivados (*defanged*), por ejemplo `kali365[.]xyz`. |
| **IDs de MITRE** | La consigna decía «no entregues IDs de MITRE ATT&CK ni ATLAS, CVE, actores, cifras ni capacidades de productos», pero la tarea 2 pide técnicas. **Mi interpretación: «no inventes».** Solo hay IDs verificados el 05/11/2026 en attack.mitre.org (ATT&CK v20.1) y en los datos oficiales de ATLAS (release 2026.10, el archivo que carga atlas.mitre.org). No atribuyo actores. Las cifras y las capacidades de productos llevan su fuente. |
| **Mapeo propio** | Arctic Wolf **no cita MITRE**. MITRE sí tiene una ficha de Kali365 (S9044) con 17 técnicas. Distingo las técnicas **asignadas por MITRE a S9044** de las **candidatas** que propongo yo. |

**Lo que leí y lo que no:**
- Leí Arctic Wolf. Del final del artículo (unos 10.000 caracteres) solo obtuve un resumen; por eso no uso los hashes que aparecen ahí.
- Leí completos la guía de CISA (extraje el texto del PDF), el aviso del FBI y la ficha S9044 de MITRE.
- Leí las páginas de Microsoft Learn que cito.
- El resto de las fuentes viene del guion de la charla, verificado el 04/11/2026.

---

## 1. Por qué (Golden Circle)

| | |
|---|---|
| **WHY: por qué importa** | Kali365 rompe una promesa en la que confían millones de personas: «si entro en la página real de Microsoft y apruebo mi MFA, estoy seguro». La víctima hace todo «bien» y aun así entrega su sesión. Lo que se protege son **personas** (la usuaria de finanzas que pierde su cuenta y carga con la culpa), **dinero** (fraude BEC), **datos personales** (de clientes y empleados) y **confianza** (los contactos que reciben phishing desde una cuenta real). En clave de IA, los **tokens delegados** son la llave con la que copilotos, agentes y conectores actúan en nombre de una persona. Quien roba el token de una persona hereda lo que su IA puede leer y hacer. |
| **HOW: cómo lo defendemos** | Cortando **la técnica, no la marca** (Kali365 ya cambió de nombre al menos una vez). Hay que cerrar el flujo de autorización que no se necesita, vigilar lo que el kit no puede esconder (inicios de sesión con código, reglas de buzón, dispositivos nuevos) y responder **revocando sesiones, no solo cambiando contraseñas**. Y gobernar las identidades de IA con mínimo privilegio, porque cada permiso delegado es parte del radio de daño. |
| **WHAT: qué entregamos** | La cadena de ataque mapeada a ATT&CK y ATLAS, una matriz de cuatro fases con responsable y plazo, los tres cortes prioritarios, el análisis crítico de las medidas del artículo, un ejercicio de mesa de 60 minutos, dos resúmenes (técnico y ejecutivo) y tres insights: el ecosistema PhaaS, dónde reportar y el marco legal colombiano. |

---

## 2. Cadena de ataque

### 2.1 Qué dice la fuente (resumen verificado)

- **[HECHO]** Desde inicios de abril de 2026, Arctic Wolf siguió una campaña masiva de phishing con código de dispositivo contra Microsoft 365.
  - **Alcance:** Norteamérica y EMEA, en manufactura, educación, gobierno, seguros, finanzas y salud, entre otros.
  - **Origen:** «principalmente desde una sola dirección IP» (216.203.20[.]95). Arctic Wolf aclara que no tiene evidencia de que sea el origen maestro de la administración.
- **[HECHO]** El kit «Kali365 Live» es multiinquilino y tiene tres niveles:
  - **Admin:** el autor del kit.
  - **Agent:** el revendedor.
  - **Client:** el afiliado que paga.

  Cada inquilino personaliza nombre, colores y temas del panel. Precio publicado: **US$250 por 30 días o US$2.000 por 365 días**. Se paga con Trocador, un procesador de criptomonedas **sin KYC**. Cada afiliado configura un bot de Telegram para alertas y recuperación de la cuenta: Telegram es su ancla de identidad. Las páginas se alojan en Cloudflare Workers. Hay un cliente de escritorio Electron para Windows y macOS (Arctic Wolf).
- **[HECHO]** Señuelos:
  - **Diseño:** 3 diseños y 34 temas (gratis y «Pro» para administradores).
  - **Asuntos:** 8 asuntos fijos, que imitan SharePoint, OneDrive, Teams, buzón de voz, DocuSign, facturas, Adobe Acrobat Sign y avisos de seguridad.
  - **Formatos:** adjuntos PDF, Word, Excel y PowerPoint.
  - **Idiomas:** 14, entre ellos el español.
  - **IA:** «Los afiliados pueden, opcionalmente, generar plantillas de correo asistidas por IA y convertir señuelos HTML en adjuntos PDF» (Arctic Wolf).
- **[HECHO]** Tras robar la cuenta, el atacante:
  - Accede al buzón, recolecta contactos y hace phishing lateral.
  - Vigila palabras clave para BEC.
  - Ejecuta acciones de administrador si la cuenta tiene privilegios.
  - En algunos casos crea reglas que mueven a otra carpeta, y marcan como leídos, los correos con «spam», «phish», «click», «link» o «SharePoint».
  - Registra un dispositivo adicional (Arctic Wolf).
- **[HECHO]** «El modo de código de dispositivo secuestra la autorización; el modo cookie secuestra una sesión web autenticada» (Arctic Wolf).
- **[HECHO]** El FBI (21/05/2026) confirma tres puntos:
  - Kali365 apareció en abril de 2026 y se distribuye sobre todo por Telegram.
  - Ofrece «señuelos de phishing generados con IA».
  - Permite al atacante entrar a Outlook, Teams y OneDrive sin contraseña ni más MFA.
- **[HECHO]** MITRE (S9044, creada el 30/07/2026) lo clasifica como malware. Le asigna 17 técnicas, entre ellas T1683.001 «Generate Content: Written Content», porque el kit usa IA «como Claude Sonnet» para evaluar correos y redactar respuestas de BEC.

### 2.2 Tabla por etapa

Columnas ATT&CK: **(S9044)** = técnica que MITRE asigna a Kali365. **(cand.)** = candidata propuesta por mí, que existe en ATT&CK pero MITRE no la asigna a este kit.
Columna ATLAS: solo para la variante de IA. **Confirmada** = la fuente documenta la conducta. **Probable** = coherente con una capacidad documentada, pero no observada. **Posible** = hipótesis.

| Etapa | Qué hace el adversario | Objeto comprometido | Activo afectado (incluye IA) | Evidencia observable | Fuente de registro | Técnica ATT&CK candidata | Técnica ATLAS (variante IA) |
|---|---|---|---|---|---|---|---|
| **1. Señuelo** | **[HECHO]** Envía un correo con enlace o adjunto (PDF, Word, Excel o PowerPoint) que imita servicios conocidos. Las plantillas pueden ser asistidas por IA y vienen en 14 idiomas. | La confianza del usuario; el buzón de entrada (todavía no hay compromiso técnico). | Persona y reputación de las marcas suplantadas. **[HIPÓTESIS]** Si el buzón alimenta un asistente de IA, el señuelo también entra a su contexto. | Asuntos de plantilla («compartió un archivo», «solicitó tu firma», «Factura #…»); enlaces a subdominios de Cloudflare Workers; PDF generado a partir de HTML. | Gateway de correo o Defender for Office 365; reportes de usuarios. | T1566.001 y T1566.002 (S9044); T1683.001 (S9044); T1583.006 y T1608.005 (cand.) | AML.T0016.002 «Obtain Capabilities: Generative AI»: **Confirmada** (Arctic Wolf, FBI). |
| **2. Página** | **[HECHO]** Página de aterrizaje ofuscada que solo se renderiza en un navegador real. Al cargarla, el backend del kit genera un código de dispositivo **legítimo** de Microsoft y muestra instrucciones para `microsoft.com/devicelogin`. | La sesión de navegador del usuario; el código generado para el cliente del atacante. | Identidad del usuario, todavía no comprometida. | Visita a un dominio externo efímero y, minutos después, a `microsoft.com/devicelogin`; texto de plantilla de «documento seguro». | Proxy web o DNS; telemetría de endpoint; navegador. | T1204.001 y T1204.004 (S9044); T1059.007 (S9044); T1090 y T1102 (S9044) | — |
| **3. Autorización** | **[HECHO]** La víctima escribe el código y se autentica, **con MFA**, en la infraestructura real de Microsoft. Así autoriza al cliente del atacante. | El otorgamiento OAuth (*device authorization grant*). | Identidad de Entra ID. La MFA se cumple, pero no protege. | Inicio de sesión con protocolo de autenticación «Device code». **[HIPÓTESIS]** La IP que canjea el token difiere de la IP de la víctima. **[HECHO, Microsoft]** Patrón: error 50199 seguido de un inicio exitoso en menos de 5 minutos. | Registros de inicio de sesión de Entra ID (interactivos y no interactivos); Entra ID Protection. | T1528 (S9044); T1078.004 (cand.) | — |
| **4. Captura de token** | **[HECHO]** El backend del kit obtiene los tokens de **acceso y de actualización**, los muestra en el panel y **puede compartirlos con otros afiliados**. | Token de acceso y token de actualización (en el modo cookie, las cookies de sesión). | La sesión de M365 (Exchange, Teams, OneDrive, SharePoint). **[HIPÓTESIS]** Todo recurso de IA al que la identidad tenga acceso con permisos delegados (asistente corporativo, agentes, conectores). | Inicios de sesión no interactivos desde la infraestructura del atacante (por ejemplo, 216.203.20[.]95, 162.243.166[.]119 o 199.91.220[.]111, puerto 8443); User-Agent `python-requests` desde ASN de hosting (Huntress). | Registros no interactivos de Entra ID; registros de actividad de Microsoft Graph. | T1528 (S9044); T1539 en el modo cookie (S9044) | AML.T0091.000 «Use Alternate Authentication Material: Application Access Token»: **Posible**. |
| **5. Uso del token** | **[HECHO]** Lee el buzón, roba contactos, hace phishing lateral y vigila palabras clave para BEC. Ejecuta acciones de administrador si el token es privilegiado; según MITRE, el módulo «Exchange Admin» crea conectores y cambia reglas de flujo. El cliente de escritorio usa el User-Agent `kali365-live/1.0.0`. **[HIPÓTESIS, variante IA]** Con el mismo token consulta el asistente de IA corporativo («resume los pagos pendientes y las cuentas bancarias de proveedores») o invoca agentes con sus herramientas. | Buzón, contactos, archivos y chats. **[HIPÓTESIS]** Las sesiones del asistente de IA. | Correos de pagos, credenciales guardadas en correos (frases semilla, claves de API), contactos. **[HIPÓTESIS]** El índice semántico y las respuestas del asistente: la IA resume en segundos lo que antes había que buscar a mano. | `kali365-live/1.0.0` en llamadas a Graph (Arctic Wolf: «alta confianza»); accesos masivos a elementos del buzón; picos de envío. **[HIPÓTESIS]** Eventos *CopilotInteraction* fuera de horario o desde IP nuevas. | Auditoría unificada de Purview; registros de actividad de Graph; Defender for Cloud Apps. **[HECHO, Microsoft]** Las interacciones con Copilot se auditan como *CopilotInteraction*. | T1550.001, T1071.001, T1087.003 y T1552.001 (S9044); T1114.002 y T1534 (cand.) | AML.T0012 «Valid Accounts» y AML.T0091.000: **Posible**. AML.T0064 «Gather RAG-Indexed Targets» y AML.T0085.000 «Data from AI Services: RAG Databases»: **Posible**. AML.T0053 «AI Agent Tool Invocation» y AML.T0086 «Exfiltration via AI Agent Tool Invocation»: **Posible**. Módulo BEC con IA: AML.T0052.000 «Spearphishing via Social Engineering LLM», **Probable** (MITRE lo documenta en el código del kit; no se sabe cuánto se usa). |
| **6. Ocultamiento y persistencia** | **[HECHO]** Reglas de bandeja que esconden los correos con «spam», «phish», «click», «link» o «SharePoint»; registro de un dispositivo adicional. El token de actualización da acceso prolongado. **[HIPÓTESIS, variante IA]** Siembra en SharePoint o en el buzón un documento con instrucciones dirigidas a un asistente o agente: persistencia que no depende del token. | Reglas del buzón; directorio de dispositivos; **[HIPÓTESIS]** el corpus que indexa la IA. | Integridad del buzón y del directorio. **[HIPÓTESIS]** Integridad del contexto de los agentes: otras personas reciben respuestas manipuladas. | Creación o cambio de reglas de bandeja con esas palabras; registro de un dispositivo poco después de un inicio con código. **[HECHO, Microsoft Purview]** El registro de auditoría de Copilot incluye un campo de detección de inyección entre prompts (*XPIADetected*). | Auditoría unificada (Exchange); registros de auditoría de Entra ID; auditoría de Copilot. | T1564.008 (S9044); T1098.005 (cand.); T1114.003 (cand.) | AML.T0051.001 «LLM Prompt Injection: Indirect», AML.T0070 «RAG Poisoning», AML.T0066 «Retrieval Content Crafting» y AML.T0080 «AI Agent Context Poisoning»: **Posible**. Es factible en M365 Copilot según los casos AML.CS0026 y AML.CS0059. |
| **7. Impacto** | **[HECHO]** BEC (desvío de pagos), phishing desde la cuenta real hacia los contactos, robo de datos y de credenciales. **[HIPÓTESIS, variante IA]** Decisiones o acciones de agentes basadas en contenido envenenado; fuga de datos por las respuestas de la IA. | Transacciones; relaciones con clientes y proveedores. | Dinero, datos personales (Ley 1581 de 2012), reputación. **[HIPÓTESIS]** Confianza en el asistente de IA. | Cambios de cuenta bancaria en conversaciones de pago; quejas de terceros; envío masivo. | Rastreo de mensajes; auditoría unificada; finanzas o ERP. | T1657 y T1684.001 «Social Engineering: Impersonation» (cand.) | AML.T0086: **Posible**. |

> **Cambios de versión que conviene saber** [HECHO, MITRE]:
> - Desde ATT&CK v19, la táctica TA0005 se llama «Stealth», no «Defense Evasion».
> - T1656 «Impersonation» ahora redirige a **T1684.001**.
> - En ATLAS 2026.10 **ya no existe AML.T0057** «LLM Data Leakage»: su contenido pasó a AML.T0085.002. Por eso no la uso.

### 2.3 Attack Flow en texto (con bifurcaciones y condiciones)

```text
ORIGEN      Afiliado que alquila «Kali365 Live» (US$250/30 días, pago sin KYC, cuenta anclada a Telegram)   [HECHO]
   ↓
VECTOR      Correo con enlace o adjunto que imita SharePoint, OneDrive, Teams, DocuSign, Adobe Sign, buzón de voz o factura
            Plantillas asistidas por IA, 14 idiomas                                                         [HECHO]
   ↓
ENTRADA     Página ofuscada en Cloudflare Workers (solo se renderiza en un navegador real)                  [HECHO]
   ↓
   ├── RAMA A · MODO CÓDIGO DE DISPOSITIVO («secuestra la autorización»)
   │     Condición: el tenant permite el device code flow para ese usuario y recurso (sin bloqueo de acceso condicional)
   │     TÉCNICA        El backend pide un código legítimo al cargar la página → instrucciones para microsoft.com/devicelogin
   │     COMPORTAMIENTO La víctima escribe el código y aprueba contraseña + MFA en el sitio REAL de Microsoft
   │     ACCIÓN         El backend recibe el token de acceso y el de actualización
   │     Nota: aquí FIDO2 o passkey se cumplen, porque el origen es legítimo        [HIPÓTESIS técnica, ver 4.2]
   │
   └── RAMA B · MODO «COOKIE LINK» / AiTM («secuestra una sesión web autenticada»)
         Condición: la MFA de la víctima es «phisheable» (contraseña + código, push o SMS)
         TÉCNICA        Proxy inverso entre la víctima y el inicio de sesión real
         COMPORTAMIENTO La víctima inicia sesión normalmente a través del proxy
         ACCIÓN         Captura de cookies de sesión y tokens
         Nota: una MFA ligada al origen (FIDO2 o passkey) rompe esta rama      [HECHO: CISA llama «resistente a phishing» a FIDO/PKI]
   ↓  (las ramas convergen)
COMPORTAMIENTO  Los tokens aparecen en el panel y pueden pasar a otros afiliados.
                El cliente Electron (User-Agent kali365-live/1.0.0) lee el buzón por Graph                     [HECHO]
   ├── Condición: la cuenta es privilegiada → acciones de administrador (conectores y reglas de flujo de Exchange)  [HECHO, MITRE]
   └── Condición: la identidad tiene un asistente de IA o agentes con permisos delegados                       [HIPÓTESIS]
         → consulta al asistente con la identidad robada (reconocimiento acelerado)
         → o siembra contenido que un agente leerá después (inyección indirecta, persistencia en el contexto)
   ↓
ACCIÓN      Reglas de bandeja que esconden los avisos; registro de un dispositivo nuevo                       [HECHO]
   ↓
IMPACTO     Fraude BEC y desvío de pagos · phishing interno desde la cuenta real · robo de datos y credenciales
            · exposición de datos personales                                                                   [HECHO]
            · respuestas o acciones de IA manipuladas                                                          [HIPÓTESIS]
```

**Condiciones de corte** (si una no se cumple, la rama muere):
- **A1.** El flujo de código está permitido.
- **A2.** El usuario escribe un código que no generó.
- **B1.** La MFA no está ligada al origen.
- **C1.** El token sigue siendo válido: no se revocó la sesión o no ha expirado.
- **C2.** La cuenta tiene permisos que valen la pena: buzón de finanzas, administración o IA con alcance amplio.

### 2.4 El puente con la seguridad de la IA: un laboratorio sobre tokens delegados

**a) La IA como acelerador del atacante.**
- **[HECHO]** Kali365 usa IA en tres puntos documentados:
  - Plantillas de correo asistidas por IA (Arctic Wolf).
  - Señuelos generados con IA (FBI).
  - Un módulo que califica correos y redacta respuestas para BEC con un modelo «como Claude Sonnet» (MITRE, T1683.001).
- **[HECHO]** En una campaña parecida, con el kit EvilTokens, Microsoft (06/04/2026) documentó «correos de phishing dirigidos y alineados con el rol de la víctima» generados con IA, y la generación del código en el momento del clic para esquivar su expiración de 15 minutos.
- **Consecuencia:** la IA quita al defensor dos señales clásicas, la mala redacción y el idioma, y abarata la personalización. Por eso la defensa se mueve a señales de **identidad** (flujo, dispositivo, token), que la IA del atacante no puede maquillar.

**b) Los tokens OAuth como activo de IA.**
- **[HECHO]** Microsoft 365 Copilot «solo muestra datos de la organización para los que cada usuario tiene al menos permisos de visualización» (Microsoft Learn). Es decir: **Copilot hereda exactamente los permisos del token del usuario**.
- **[HECHO]** Las APIs de Microsoft 365 Copilot funcionan solo con **permisos delegados**. La Retrieval API está en v1.0 y la Chat API en versión preliminar. Por ser endpoints de Graph, el acceso condicional se les aplica automáticamente (Microsoft Learn).
- **[HECHO]** La especificación de autorización de MCP (versión 2026-07-28) se basa en OAuth 2.1:
  - Exige que el servidor valide que el token fue emitido **para él** (audiencia).
  - Prohíbe de forma explícita el *token passthrough*: reenviar a otra API el token recibido.
  - Pide tokens de acceso de vida corta.
- **Lectura:** en un ecosistema de copilotos, agentes y servidores MCP, **el token es la identidad operativa de la IA**. Robar el token de una persona equivale a robar la capacidad de su IA de leer y actuar.

**c) Herramientas legítimas que usan el código de dispositivo.**
- **[HECHO]** Dependen de Entra ID, así que un bloqueo en Entra las afecta:
  - Azure CLI (`--use-device-code`, y como respaldo automático si no hay navegador).
  - Azure PowerShell y Microsoft Graph PowerShell (`-UseDeviceAuthentication`).
  - Los dispositivos Teams Android, teléfonos y paneles. Microsoft dice expresamente «no bloquear el flujo de código de dispositivo» para ellos.
- **[HECHO]** GitHub CLI y AWS CLI (IAM Identity Center) usan *device flows* propios. No pasan por Entra ID.
- **[HECHO]** La especificación de MCP no incluye el *device authorization grant*.
- **[HIPÓTESIS por verificar]** Algunos agentes de IA, CLIs de IA o scripts sin navegador que acceden a recursos protegidos por Entra podrían autenticarse con código de dispositivo. Un bloqueo total los rompería.
- **Cómo verificarla sin suposiciones:** filtrar 30 días de registros de inicio de sesión por «Authentication protocol = Device code», agrupar por aplicación y usuario, y crear la política en modo «solo informe» antes de activarla (Microsoft Learn).

**d) La hipótesis central: ¿qué cambia si el atacante usa la IA de la víctima?**

**[HIPÓTESIS]** Con el token robado, el atacante:
1. Consulta el asistente de IA corporativo conectado al correo y a SharePoint.
2. O deja contenido (un correo, un documento) que después lee un agente: inyección indirecta de prompts.

| Dimensión | Sin IA en el entorno | Con asistente o agentes conectados |
|---|---|---|
| **Velocidad del reconocimiento** | El atacante lee y busca a mano, o con palabras clave (Kali365 ya lo automatiza). | Una pregunta en lenguaje natural resume pagos, proveedores y credenciales en todo el corpus que el usuario puede ver. **El radio de daño es el mismo, pero se recorre en segundos.** |
| **Alcance** | El buzón y los archivos del usuario. | Igual en teoría (Copilot respeta los permisos). En la práctica, el **sobre-compartimiento** de SharePoint, que Copilot hace fácil de encontrar, se vuelve visible para el atacante. [HIPÓTESIS] |
| **Persistencia** | Reglas, dispositivo y token de actualización. Se corta con la revocación y la limpieza. | **El contenido sembrado sobrevive a la revocación del token.** Sigue envenenando respuestas para otras personas o disparando acciones de agentes (AML.T0070, AML.T0080). [HIPÓTESIS, factible según AML.CS0026 y AML.CS0059] |
| **Impacto** | BEC desde la cuenta comprometida. | Además, BEC «desde dentro» de la IA: un agente que, por instrucciones sembradas, cambia datos de pago o resume mal un procedimiento. [HIPÓTESIS] |
| **Detección** | Registros de Entra ID, Exchange y Graph. | Hace falta **una fuente más**: la auditoría de Copilot (*CopilotInteraction*, con campos como *AccessedResources*, *XPIADetected* y *JailbreakDetected*). Sin ella, el SOC no ve qué se le preguntó a la IA. El contenido sembrado es texto «normal» y los filtros de malware no lo ven. |
| **Respuesta** | Revocar, limpiar, rehacer la MFA. | Además: revisar qué recuperó el asistente, poner en cuarentena el contenido sembrado, revisar los consentimientos de agentes y apps, y rotar las credenciales que la IA pudo haber resumido. |

- **Precedente** [HECHO]: CVE-2025-32711 («EchoLeak»), «inyección de comandos de IA en M365 Copilot».
  - **Severidad:** CVSS 9.3 según Microsoft como CNA.
  - **Corrección:** «ya mitigada por completo por Microsoft», sin acción para los clientes.
  - **ATLAS:** la documenta como caso AML.CS0059.
  - **Lo que demuestra:** que el contenido que llega por correo puede convertirse en instrucciones para un asistente.
  - **Lo que no demuestra:** que Kali365 lo haya hecho. Por eso la variante IA de este reporte sigue siendo **hipótesis**.

---

## 3. Matriz Prevenir / Detectar / Contener / Recuperar

### 3.1 Por etapa de la cadena

| Etapa | Prevenir | Detectar | Contener | Recuperar |
|---|---|---|---|---|
| **1. Señuelo** | Capacitación en concienciación de seguridad (**Artículo**). DMARC en «reject», filtrado de URL y DNS de protección (**Otra fuente: CISA**). | Botón de reporte en Outlook y canal interno; vigilancia del correo interno (**Otra fuente: CISA**). | Purgar el mensaje de todos los buzones que lo recibieron (**Propuesto**). | Enviar el ejemplo real (sin nombres) como material de la siguiente capacitación (**Propuesto**). |
| **2. Página** | Regla de oro: «nunca escribas un código que no generaste en un equipo frente a ti» (**Propuesto**, a partir del mecanismo del FBI y el artículo). | Visita a `microsoft.com/devicelogin` justo después de un dominio externo desconocido (**Propuesto**). | Bloquear en proxy o DNS los dominios e IP publicados (**Propuesto**, con IOC del **Artículo**). | — |
| **3. Autorización** | Bloquear el device code flow con acceso condicional (**Artículo**) y la transferencia de autenticación (**Otra fuente: FBI y Microsoft**). Restringir las excepciones por red de confianza, plataforma o grupo (**Artículo**). Políticas de riesgo de inicio de sesión de Entra ID Protection (**Artículo**). | Revisión de los inicios de sesión por device code (**Artículo**, como revisión puntual del MDR); convertirla en alerta continua (**Propuesto**). | Activar el bloqueo si no estaba activo y suspender temporalmente la cuenta (**Otra fuente: Microsoft**). | Dejar el bloqueo como estado permanente, con excepciones con dueño y fecha de caducidad (**Propuesto**). |
| **4. Captura de token** | Protección de tokens (vinculación al dispositivo) donde esté soportada: apps nativas de Windows, macOS e iOS para Exchange, SharePoint y Teams (**Otra fuente: Microsoft**; su efecto contra Kali365 es **[HIPÓTESIS]**). | Inicios no interactivos desde las IP del IOC (**Artículo**) o desde ASN de hosting con User-Agent `python-requests` (**Otra fuente: Huntress**). | Revocar las sesiones (revokeSignInSessions) **y** deshabilitar la cuenta: los tokens de acceso siguen activos hasta 1 hora (**Otra fuente: Microsoft**). | Volver a registrar la MFA después de revocar (**Otra fuente: Microsoft**; también lo pide SpyCloud). |
| **5. Uso del token** | Mínimo privilegio y licencias de IA solo para quien las usa; inventario de consentimientos OAuth (**Propuesto**). | User-Agent `kali365-live/1.0.0` (**Artículo**); acceso masivo al buzón; picos de envío (**Otra fuente: Huntress**). | Revocar los consentimientos de apps desconocidas (**Otra fuente: Barracuda**). | Auditar qué se leyó y qué se envió (**Otra fuente: CISA**, «auditar el acceso a la cuenta»). |
| **5-IA. Uso del token contra la IA** [HIPÓTESIS] | Los agentes con permisos delegados acotados y aprobación humana para acciones sensibles (pagos, reenvíos); sin *token passthrough* en servidores MCP (**Otra fuente: especificación MCP**; el resto, **Propuesto**). | Revisar la auditoría *CopilotInteraction*: interacciones fuera de horario, desde IP nuevas, o con *XPIADetected* o *JailbreakDetected* en verdadero (**Propuesto**, sobre una capacidad documentada por Microsoft). | Revocar los permisos y sesiones de agentes y apps ligados a la cuenta; poner en cuarentena el contenido sospechoso que indexa la IA (**Propuesto**). | Revisar qué recuperó el asistente (*AccessedResources*) y rotar las credenciales mencionadas (**Propuesto**). |
| **6. Ocultamiento y persistencia** | Alertas por defecto sobre reglas de bandeja y registro de dispositivos (**Propuesto**). | Reglas nuevas con «spam», «phish», «click», «link» o «SharePoint»; dispositivo nuevo tras un inicio por código (**Artículo**, como conducta observada). | Exportar la evidencia y luego borrar las reglas y los reenvíos; deshabilitar el dispositivo (**Otra fuente: Microsoft**). | Revisión de todos los buzones con la misma regla (búsqueda en el tenant) (**Propuesto**). |
| **7. Impacto** | Confirmar fuera de banda (por teléfono) todo cambio de cuenta bancaria (**Propuesto**). | Finanzas como sensor: un cambio de cuenta pedido por correo dispara la verificación (**Propuesto**). | Congelar los pagos en curso; avisar al banco (**Propuesto**). | Avisar a clientes y proveedores; denunciar; reportar a la SIC si hay datos personales comprometidos (**Propuesto**, con canales verificados en la sección 8). |

### 3.2 Plan por fase: acción, responsable, momento, evidencia y origen

#### Prevenir (antes)

| # | Acción concreta | Responsable | Momento | Evidencia de que se hizo | Origen |
|---|---|---|---|---|---|
| P1 | Inventariar 30 días de inicios de sesión con «Authentication protocol = Device code», por aplicación y usuario. | TI / identidad | Esta semana | Exportación con fecha y lista de usos legítimos, cada uno con dueño. | Otra fuente (FBI: auditar antes; Microsoft Learn: filtro de protocolo) |
| P2 | Directiva de acceso condicional que bloquee el device code flow y la transferencia de autenticación para todos los usuarios y recursos. Excluir las cuentas de emergencia y Device Registration Service, si aplica. Primero en «solo informe», luego activa. | TI / identidad, con aprobación de gerencia | Esta semana (informe) → la siguiente (activa) | Directiva en estado «Activado»; resultados del modo informe revisados; lista de exclusiones. | Artículo (bloqueo) + otra fuente (FBI y Microsoft: transferencia, exclusiones, solo informe) |
| P3 | Excepciones mínimas: cuentas de recurso de Teams en un grupo, limitado por red de confianza o plataforma. | TI / identidad | Junto con P2 | Grupo con miembros nominales; condición de red o plataforma documentada. | Artículo (restringir por red, plataforma o grupo) |
| P4 | Activar las políticas de riesgo de inicio de sesión de Entra ID Protection, según la licencia. | TI / identidad | Esta semana | Directivas activas; prueba con un inicio riesgoso simulado en un entorno de pruebas. | Artículo |
| P5 | MFA resistente a phishing (FIDO2 o passkeys) para administradores, finanzas y gerencia. | TI + gerencia (presupuesto) | Este trimestre | Porcentaje de cuentas privilegiadas con fuerza de autenticación resistente a phishing. | Otra fuente (CISA: priorizar administradores y cuentas con acceso a datos financieros) |
| P6 | Capacitación con la regla de oro y simulacro **sin** códigos reales (solo reconocimiento y reporte). | Gerencia + TI | Este mes y cada año | Asistencia y evaluación de conocimientos; tasa de reporte del simulacro. | Artículo (capacitación) + otra fuente (CISA: anual con evaluación) |
| P7 | Inventario de asistentes de IA, agentes y apps con consentimiento OAuth; quitar los permisos sin dueño. | Responsable de IA | Este mes | Lista con app, permisos, dueño y fecha de revisión. | Propuesto |
| P8 | Política de pagos: todo cambio de cuenta bancaria se confirma por teléfono a un número conocido. | Gerencia + finanzas | Desde hoy | Procedimiento firmado y registro de confirmaciones. | Propuesto |

#### Detectar (en minutos)

| # | Acción concreta | Responsable | Momento | Evidencia de que se hizo | Origen |
|---|---|---|---|---|---|
| D1 | Reportar con el botón de Outlook o al canal interno cualquier código recibido o inicio de sesión inesperado. | Usuario | En el momento | Reportes recibidos y tiempo hasta el reporte. | Otra fuente (CISA) |
| D2 | Alerta continua por inicios de sesión con device code fuera de las excepciones. | SOC o MDR | Siempre activa | Regla de detección activa y alerta de prueba atendida. | Artículo (revisión) → Propuesto (alerta continua) |
| D3 | Alerta por reglas de bandeja nuevas que mueven, marcan como leído o borran correos con palabras de seguridad. | SOC | Siempre activa | Regla activa; búsqueda retroactiva de 90 días ejecutada. | Artículo (conducta observada) + Propuesto (alerta) |
| D4 | Alerta por registro de dispositivo dentro de los 30 minutos siguientes a un inicio con device code. | SOC | Siempre activa | Regla activa. | Propuesto (sobre la conducta documentada por el Artículo y Microsoft) |
| D5 | Búsqueda retroactiva de IOC: `kali365-live/1.0.0`, `kali365[.]xyz`, `v2.kali365[.]xyz`, `api.kali365[.]xyz`, 216.203.20[.]95, 162.243.166[.]119 y 199.91.220[.]111 (puerto 8443). | SOC | Esta semana, una vez | Informe de búsqueda, con o sin resultados. | Artículo (IOC) |
| D6 | Revisión semanal de la auditoría *CopilotInteraction*: interacciones fuera de horario, desde IP nuevas, o con *XPIADetected* o *JailbreakDetected*. | Responsable de IA + SOC | Cada semana | Informe semanal firmado. | Propuesto |
| D7 | Finanzas trata todo cambio de cuenta bancaria pedido por correo como un posible incidente. | Finanzas | Siempre | Registro de verificaciones. | Propuesto |

#### Contener (la primera hora)

| # | Acción concreta | Responsable | Momento | Evidencia de que se hizo | Origen |
|---|---|---|---|---|---|
| C1 | Declarar el incidente; preservar el correo (encabezados y cuerpo), la hora, la IP y la ubicación. | Mesa de ayuda / SOC | Minuto 0 a 5 | Ticket con evidencias adjuntas. | Otra fuente (FBI: qué guardar) |
| C2 | Revocar las sesiones (revokeSignInSessions) **y** deshabilitar la cuenta temporalmente. | TI / identidad | Antes de 15 minutos | Marca de tiempo de la revocación; cuenta deshabilitada. | Otra fuente (Microsoft) |
| C3 | Exportar y luego borrar las reglas de bandeja, los reenvíos y los conectores raros; deshabilitar los dispositivos desconocidos. | TI / identidad | Primera hora | Antes y después documentados. | Otra fuente (Microsoft; el FBI pide reportar dispositivos y sesiones) |
| C4 | Activar el bloqueo del device code flow si aún no existía, con las exclusiones mínimas. | TI / identidad | Primera hora | Directiva activa. | Artículo |
| C5 | Buscar el mismo patrón en otros usuarios (mismos IOC, mismo asunto, misma IP). | SOC | Primera hora | Lista de cuentas revisadas. | Propuesto |
| C6 | Revocar los consentimientos y permisos que la cuenta dio a apps, agentes o asistentes de IA; poner en cuarentena los documentos sospechosos recién creados que indexa la IA. | Responsable de IA | Primera hora | Lista de permisos revocados y contenido en cuarentena. | Propuesto |
| C7 | Congelar los pagos con cambios de cuenta; llamar al banco si ya salió dinero. | Finanzas + gerencia | Primera hora | Registro de llamadas. | Propuesto |

#### Recuperar (los días siguientes)

| # | Acción concreta | Responsable | Momento | Evidencia de que se hizo | Origen |
|---|---|---|---|---|---|
| R1 | Volver a registrar la MFA (resistente a phishing si es posible) y la contraseña; rehabilitar la cuenta. | TI / identidad | Día 1 | Métodos de MFA nuevos; fecha de rehabilitación. | Otra fuente (Microsoft; CISA: «reaprovisionar» las cuentas) |
| R2 | Auditar qué se leyó, qué se envió y qué se descargó. | SOC | Días 1 a 3 | Informe de alcance. | Otra fuente (CISA) |
| R3 | Revisar qué recuperó el asistente de IA con esa identidad (*AccessedResources*) y rotar las credenciales mencionadas. | Responsable de IA | Días 1 a 3 | Lista de recursos y credenciales rotadas. | Propuesto |
| R4 | Avisar por teléfono a clientes y proveedores que recibieron correos de la cuenta. | Gerencia + finanzas | Día 1 | Lista de contactos avisados. | Propuesto |
| R5 | Denuncia penal y reportes externos; evaluar el reporte a la SIC si hubo datos personales comprometidos (sección 8.2). | Gerencia + legal | Primeros días; a la SIC, dentro de los 15 días hábiles siguientes a la detección | Números de radicado. | Otra fuente (SIC, Circular Única) + Propuesto (orden) |
| R6 | Reunión sin culpables; actualizar el plan, las alertas y la capacitación. | Gerencia + TI + personal | A las 2 semanas | Acta con cambios, dueño y fecha. | Propuesto |

---

## 4. ¿Dónde rompemos la cadena?

### 4.1 Tres puntos priorizados

| Prioridad | Punto de corte | Impacto | Urgencia | Esfuerzo | Por qué |
|---|---|---|---|---|---|
| **1** | **Etapa 3, autorización:** bloquear el device code flow y la transferencia de autenticación con acceso condicional, con excepciones nominales. | Alto: elimina la rama A completa, la técnica principal. | Alta: la campaña sigue activa con otras marcas, no hay parche y Microsoft lo trata como función legítima. | Bajo a medio: una directiva, más la auditoría previa. | Es el único punto donde **la decisión correcta no depende de la persona**. Microsoft recomienda bloquear «siempre que sea posible» y acercarse «lo más posible a un bloqueo unilateral». Según la página de valores predeterminados de seguridad, desde el 01/07/2026 todos los tenants nuevos de Entra bloquean el device code flow. |
| **2** | **Etapas 4 a 6, uso y persistencia:** detección continua (código, reglas y dispositivos) más el playbook de contención (revocar **y** deshabilitar). | Alto: limita el daño de lo que pase por las excepciones y de la rama B. | Alta: Huntress midió accesos persistentes en apenas 42 segundos. | Medio: alertas más un procedimiento ensayado. | El token es portador (*bearer*): quien lo tiene, entra. Cambiar la contraseña no lo invalida; revocarlo sí, y deshabilitar cubre la hora de vida del token de acceso. |
| **3** | **Etapas 5-IA y 7, impacto:** verificación fuera de banda de los pagos y mínimo privilegio para la IA (inventario de consentimientos, permisos delegados acotados, aprobación humana en acciones sensibles). | Alto en dinero y en confianza. | Media. | Bajo (pagos) a medio (IA). | Aunque la identidad caiga, el fraude necesita que **alguien pague** o que **un agente actúe**. Son los dos frenos humanos más baratos del final de la cadena. |

### 4.2 Por qué la MFA tradicional no basta

1. **[HECHO]** En la rama A, la víctima completa la MFA de verdad en el sitio legítimo de Microsoft. La MFA demuestra **quién** se autentica, no **para qué dispositivo** se emite el token. El RFC 8628 (sección 5.4, «Remote Phishing») lo reconoce: el flujo puede iniciarse en un dispositivo en manos del atacante, y las mitigaciones del estándar «no impiden que un *phisher* presente un código nuevo, en particular si interactúa con el usuario en tiempo real».
2. **[HECHO]** El resultado es un token **portador**: el atacante entra «sin necesitar una contraseña ni completar más verificaciones MFA» (FBI).
3. **[HIPÓTESIS técnica]** FIDO2 o una passkey protegen porque ligan la autenticación al **origen**. En la rama B, el origen falso del proxy no coincide, así que la rompen. En la rama A, el origen sí es `microsoft.com`, así que la autenticación pasa. Ninguna fuente leída lo afirma de forma literal: es una inferencia sobre el funcionamiento de FIDO2. Por eso el bloqueo del flujo va primero y la MFA resistente a phishing va como segunda capa.
4. **[HECHO]** La guía de CISA (octubre de 2023) considera FIDO o PKI «resistentes a phishing y resilientes ante las amenazas» que describe. Pero **no menciona tokens, OAuth, sesiones ni el código de dispositivo**: se escribió para el robo de credenciales y el malware. **Brecha:** seguir la guía al pie de la letra no cierra el ataque de Kali365.

---

## 5. Análisis de cada medida del artículo

| Medida del artículo | Qué corta | Qué no cubre | Efectos colaterales | Brechas y propuesta del analista |
|---|---|---|---|---|
| **1. Bloquear el device code flow con acceso condicional** (todos los usuarios → todas las apps → flujos de autenticación → código de dispositivo → bloquear) | La rama A en la etapa 3: el token no se emite. | **La rama B (AiTM).** Los tokens **ya robados**, porque bloquear no revoca. La **transferencia de autenticación**. Las cuentas excluidas. Las entidades de servicio: las directivas de usuarios no las bloquean (Microsoft Learn). Los tenants sin licencia de acceso condicional. | Rompe usos legítimos: dispositivos Teams Android, teléfonos y paneles (Microsoft: «no bloquear»); Azure CLI, Azure PowerShell y Graph PowerShell en modo código; y **[HIPÓTESIS]** CLIs o agentes de IA sin navegador. | Auditar 30 días antes; modo solo informe; excepciones con dueño y caducidad; bloquear también la transferencia (FBI); alertar sobre los intentos bloqueados, porque son señal de campaña. |
| **2. Si se necesita, restringir por red de confianza, plataforma o grupo** | Reduce la superficie a casos conocidos. | Las cuentas del grupo exceptuado siguen siendo «phisheables». **[SUPOSICIÓN por verificar]** Qué IP evalúa la condición de ubicación en este flujo, la del canje del token o la del navegador de la víctima: no lo encontré documentado. | Gestión de excepciones; riesgo de que el grupo crezca sin control. | Excepciones solo para cuentas de recurso (salas), nunca para personas; revisión mensual; alerta sobre cualquier uso fuera de la red de confianza. |
| **3. Políticas de riesgo de inicio de sesión de Entra ID Protection** | Puede bloquear o exigir más verificación cuando el inicio se evalúa como riesgoso. | Si el riesgo no se detecta, no actúa. Exigir MFA como remedio **no sirve** cuando la víctima ya está haciendo la MFA. Depende de la licencia. | Falsos positivos, por ejemplo con viajeros. | Usar «bloquear» para el riesgo alto, no solo «exigir MFA». Unirlo a la revocación automática de sesiones. |
| **4. Capacitación (recomendada «fuertemente»)** | Las etapas 1 a 3, si la persona no escribe el código. | La tasa de clics nunca es cero; la IA produce señuelos sin errores en 14 idiomas. | Fatiga y culpa, que reducen el reporte. | Una regla concreta, más el reporte sin castigo, más simulacros sin códigos reales. Medir la **tasa de reporte**, no solo la de clics. |
| **5. Para clientes del MDR: pedir una revisión de los inicios de sesión por device code** | Detecta compromisos ya ocurridos (etapas 4 a 6). | Es **puntual y reactiva**; no ve la rama B. | Ninguno relevante. | Convertirla en una **alerta continua** con playbook (revocar + deshabilitar + limpiar). |

**Lo que el artículo no dice** (brechas que cubre este reporte, todas **Propuesto** salvo que se indique):
- Revocar las sesiones y deshabilitar la cuenta (es Microsoft quien lo recomienda).
- Bloquear la transferencia de autenticación (FBI).
- La MFA resistente a phishing contra la rama B (CISA y Microsoft).
- Vigilar como alerta las reglas de bandeja y los dispositivos, que el artículo solo describe como conducta observada.
- Controles de proceso contra el BEC.
- Cualquier medida para la IA: inventario de consentimientos, auditoría *CopilotInteraction*, contenido sembrado.

---

## 6. Ejercicio de mesa (tabletop) de 60 minutos

**Objetivo:** practicar las **decisiones** ante un compromiso de Microsoft 365 por código de dispositivo, con variante de IA, y medir tiempos y roles. **No se ejecuta ningún ataque**, no se generan códigos reales y no se usa infraestructura del adversario. Todo es papel, registros ficticios y conversación.

**Roles:**
- Facilitador.
- Cronometrista y observador.
- Usuaria afectada (finanzas).
- Mesa de ayuda.
- Identidad (administración de Entra ID).
- SOC o MDR.
- Gerencia.
- Responsable de IA.
- Legal y datos personales.
- Comunicaciones (opcional).

**Agenda:** 0 a 5 min, contexto y reglas · 5 a 50 min, seis inyecciones de unos 7 minutos · 50 a 60 min, cierre.

**Escenario base (ficticio):** empresa de servicios con 60 personas, Microsoft 365 y un asistente de IA conectado al correo y a SharePoint. El device code flow **no** está bloqueado.

| # | Minuto | Inyección | Decisión que se pide | Evidencia disponible (ficticia) | Respuesta esperada |
|---|---|---|---|---|---|
| I1 | 5 | La usuaria de finanzas llama: «Abrí una factura de un proveedor, escribí un código en la página de Microsoft y me salió *Documento expirado*». Pasaron 20 minutos. | ¿Es un incidente? ¿Quién lo lidera? ¿Qué se le pide a la usuaria? | El correo reportado; la hora aproximada. | Declarar el incidente y asignar un líder. Preservar el correo (no borrarlo). Agradecer el reporte, sin culpa. Buscar a otros destinatarios del mismo correo. |
| I2 | 12 | Identidad encuentra un inicio con protocolo «Device code» exitoso desde una IP de hosting extranjera, después de un error 50199. Luego hay inicios no interactivos continuos desde otra IP. En Graph aparece el User-Agent `kali365-live/1.0.0`. | ¿Cambiar la contraseña o revocar? ¿En qué orden? | Extractos de los registros de inicio de sesión y de Graph. | Revocar las sesiones **y** deshabilitar la cuenta, porque el token de acceso dura hasta 1 hora. La contraseña y la MFA, después. Buscar el mismo patrón en todo el tenant. |
| I3 | 19 | Aparece una regla de bandeja «mover a RSS y marcar como leído» si el correo contiene *spam*, *phish* o *link*, y un dispositivo registrado 8 minutos después del inicio. | ¿Limpiar ya o preservar primero? | Auditoría unificada y auditoría de Entra ID. | Exportar la evidencia y **luego** limpiar. Deshabilitar el dispositivo. Revisar reenvíos, conectores y consentimientos de apps. |
| I4 | 26 | **(IA)** La auditoría de Copilot muestra que esa cuenta, a las 2:10 a. m., pidió «resumir los pagos pendientes a proveedores con sus cuentas bancarias». En SharePoint aparece un documento nuevo, «Procedimiento de pagos actualizado», creado con esa cuenta. | ¿Cuál es el alcance? ¿El documento es evidencia o un riesgo activo? ¿Quién decide? | Registros *CopilotInteraction* (*AccessedResources*); metadatos del documento. | Tratar al asistente como una superficie más: listar los recursos que recuperó. Poner el documento en cuarentena, sin borrarlo, porque es evidencia. Revisar quién más lo abrió o si un agente lo procesó. Rotar las credenciales mencionadas. Avisar a legal por los datos personales. Etiquetar el análisis como **hipótesis** hasta confirmar. |
| I5 | 33 | Un cliente llama: pagó una factura a la «nueva cuenta» que le indicaron desde el correo de la usuaria. | ¿Qué hacen finanzas, gerencia y legal? | El correo enviado y el comprobante del cliente. | Llamar al banco de inmediato. Denuncia penal. Llamar a los demás clientes y proveedores afectados. Preservar la evidencia. Evaluar el reporte a la SIC si hubo datos personales expuestos. |
| I6 | 40 | La gerencia pregunta si bloquean ya el código para todos. TI advierte que dos salas de Teams Android y un script de administración con Azure CLI usan el código de dispositivo. | ¿Bloquear ya o esperar? ¿Con qué excepciones? | Inventario rápido de inicios por código de los últimos 30 días. | Bloquear **ya** con exclusiones nominales temporales: las cuentas de recurso de las salas, limitadas por red, y las cuentas de emergencia. El script migra a un método sin código. En un incidente activo, dejar la directiva en solo informe no basta. Fijar fecha para revisar las excepciones. |

**Criterios de evaluación:**
1. Tiempo desde el reporte hasta la revocación y deshabilitación: meta de menos de 15 minutos.
2. Orden correcto: revocar y deshabilitar **antes** de cambiar la contraseña.
3. Evidencia preservada antes de limpiar.
4. Búsqueda activa de otros afectados.
5. Cada decisión con un dueño con nombre.
6. Comunicaciones que separan hechos de hipótesis.
7. Los activos de IA se incluyeron en el alcance.
8. Los canales externos correctos (sección 8.2).

**Preguntas de cierre:**
1. ¿Qué parte del ataque **no** habríamos visto con los registros que tenemos hoy?
2. Si el bloqueo del código hubiera estado activo, ¿en qué inyección termina el ejercicio?
3. ¿Quién es hoy el dueño de la auditoría de la IA? ¿Alguien la revisa?
4. ¿Qué cambia en el plan si la víctima es un administrador?
5. ¿Qué haríamos distinto si el ataque llega por la rama B (AiTM)?

---

## 7. Documentar

### 7.1 Versión técnica (máximo 15 líneas)

> **Kali365 (MITRE S9044)** es un PhaaS por suscripción (US$250 por 30 días) que abusa del OAuth 2.0 Device Authorization Grant de Microsoft Entra ID (rama A) o de un proxy AiTM (rama B).
> En la rama A, la víctima completa contraseña y MFA en `microsoft.com/devicelogin` y autoriza el cliente del atacante. El kit obtiene tokens de acceso y de actualización portadores, compartibles entre afiliados (T1528).
> Después usa Graph con el User-Agent `kali365-live/1.0.0` (T1550.001, T1071.001), esconde avisos con reglas de bandeja (T1564.008), registra dispositivos y prepara fraude BEC con IA (T1683.001).
> No es una vulnerabilidad: Microsoft trata el flujo como legítimo y recomienda bloquearlo «siempre que sea posible». El RFC 8628 §5.4 ya advertía este phishing remoto.
> **Controles prioritarios:**
> 1. Acceso condicional que bloquee el device code flow y la transferencia de autenticación, con excepciones nominales (cuentas de recurso de Teams, cuentas de emergencia, DRS), tras 30 días de auditoría y modo solo informe.
> 2. Detección continua de inicios con protocolo deviceCode, reglas de bandeja con palabras de seguridad y registro de dispositivos poco después del inicio.
> 3. Playbook: revokeSignInSessions más deshabilitar la cuenta (tokens de acceso de hasta 1 hora), limpieza y nuevo registro de la MFA.
> La MFA resistente a phishing rompe la rama B, no la A. Variante IA [hipótesis]: el token da acceso delegado a Copilot y a los agentes. Sumar la auditoría *CopilotInteraction* y el inventario de consentimientos OAuth.

### 7.2 Versión ejecutiva (máximo 10 líneas, sin jerga)

> Unos delincuentes alquilan por unos US$250 al mes un kit que roba cuentas de Microsoft 365 sin necesitar la contraseña.
> El empleado recibe un correo de «documento compartido», escribe un código en la página real de Microsoft y, sin saberlo, le abre la puerta al atacante.
> La verificación en dos pasos no lo frena, porque es la propia persona quien aprueba la entrada.
> Con la cuenta, el atacante lee el correo, esconde las alertas y busca desviar pagos. Si la empresa usa un asistente de IA, también puede preguntarle por la información sensible.
> No hay un parche que esperar: Microsoft recomienda desactivar ese tipo de inicio de sesión donde no se use.
> Pedimos tres decisiones: 1) que TI lo desactive esta semana, con excepciones aprobadas; 2) una regla de pagos: todo cambio de cuenta bancaria se confirma por teléfono; 3) que el personal reporte en minutos y sin miedo a sanciones.
> Si alguien cae, cambiar la contraseña no basta: TI debe cerrar todas las sesiones, y la empresa debe denunciar y evaluar su deber de reportar datos personales.

---

## 8. Insights del tablero

### 8.1 Ecosistema del cibercrimen y modelo PhaaS

- **[HECHO]** Kali365 funciona como un software empresarial: multiinquilino, con tres niveles (autor, revendedor y afiliado), marca blanca, suscripción (US$250 por 30 días o US$2.000 al año), soporte de identidad por Telegram y pago en cripto sin KYC (Arctic Wolf). Los tokens robados son una **mercancía** que circula entre afiliados.
- **[HECHO]** La barrera de entrada es baja. El FBI dice que el kit ofrece a delincuentes con poca experiencia señuelos generados con IA, plantillas automáticas y tableros de seguimiento. SpyCloud (un solo proveedor) reporta más de 500 afiliados en unos tres meses.
- **[HECHO]** La marca es desechable. Tras la alerta del FBI, los operadores anunciaron su «retiro», pero la captura de cuentas siguió con otra marca (SpyCloud). Microsoft documentó la misma técnica en otro kit, EvilTokens.
- **Conexión con los apuntes 07** (BKA y First Light):
  - Las fichas del BKA muestran que Trickbot o Black Basta se organizaban como empresas, con fundador, desarrolladores, infraestructura y proveedores de acceso.
  - First Light 2026 (INTERPOL) muestra la otra cara: el fraude por ingeniería social, el BEC y el lavado.
  - Kali365 une las dos: **un desarrollador vende la herramienta y miles de afiliados hacen ingeniería social para cometer BEC**.
  - La lección es la misma: cortar en el eslabón temprano y barato. Allá era el *loader*; aquí es el **flujo de autorización**. Y frenar el dinero, con la verificación de pagos.
- **Papel de la IA:**
  - Del lado ofensivo, abarata la personalización. Es **[HECHO]** en las plantillas y el módulo BEC.
  - Del lado defensivo, obliga a mover la detección del contenido del correo a las señales de identidad. Esto es **[HIPÓTESIS]** de trabajo, coherente con la sección 2.4.

### 8.2 Dónde reportar (Colombia primero, alternativas internacionales)

Todos los canales se verificaron el 05/11/2026 en los sitios oficiales que se enlazan.

| Situación | Canal | Quién reporta | Cuándo | Cómo | Fuente |
|---|---|---|---|---|---|
| Un empleado recibió el correo o escribió el código | **Canal interno de TI** y botón **«Reportar» de Outlook**, si la organización lo activó | Usuario | En el momento | Botón «Report → Report phishing». Los reportes van al buzón de reportes de la organización, a Microsoft o a ambos. | [HECHO] Microsoft Learn: https://learn.microsoft.com/en-us/defender-office-365/submissions-outlook-report-messages |
| TI quiere enviar a Microsoft muestras, URL o la app OAuth abusiva | **Microsoft Defender, página Submissions**; **MSRC** para abuso en servicios de Microsoft, incluida la categoría «OAuth Application» | TI / SOC | Día 0 | https://security.microsoft.com/reportsubmission · https://msrc.microsoft.com/report/ | [HECHO] Microsoft Learn y MSRC |
| Incidente cibernético en cualquier organización (pública o privada) | **ColCERT**, Equipo de Respuesta a Emergencias Cibernéticas de Colombia (MinTIC) | TI / CISO | Día 0 a 1 | Incidentes: contacto@colcert.gov.co. Phishing: phishing-report@colcert.gov.co, reenviando el correo con encabezados. Tel. +57 601 344 2222. | [HECHO] https://www.colcert.gov.co/800/w3-article-198656.html · https://www.colcert.gov.co/800/w3-propertyvalue-386766.html |
| Delito informático: orientación y reporte a la Policía | **Centro Cibernético Policial** (DIJIN) y **CAI Virtual** | Víctima o empresa | Lo antes posible | https://caivirtual.policia.gov.co. **Hoy el portal está en mantenimiento** y atiende por WhatsApp 323 273 3411. Correo: caivirtual@correo.policia.gov.co. | [HECHO] https://www.policia.gov.co/ciberseguridad |
| Denuncia penal en línea | **¡ADenunciar!** (Policía y Fiscalía): incluye «delitos informáticos» | Víctima o representante legal | Primeros días | https://adenunciar.policia.gov.co/Adenunciar/ (la raíz del dominio dio error el 05/11). Envía un número de incidente; en 24 horas informa el número de noticia criminal (NUNC), si procede. | [HECHO] https://www.policia.gov.co/sistema-nacional-denuncia-virtual-adenunciar |
| Denuncia penal (alternativa) | **Fiscalía General de la Nación**: Denuncia Fácil, línea 122 desde celular o 01 8000 9197 48, o en persona | Víctima o representante legal | Primeros días | https://sicecon.fiscalia.gov.co/denuncia/ingresoPrincipal | [HECHO] https://www.fiscalia.gov.co/servicios-de-informacion-al-ciudadano/donde-y-como-denunciar/ |
| Se comprometieron datos personales de clientes o empleados | **SIC**, Delegatura para la Protección de Datos Personales: reporte de incidentes de seguridad | La empresa, como responsable (o el encargado) del tratamiento | **Dentro de los 15 días hábiles** siguientes a la detección | En el RNBD si la empresa está obligada a inscribirse; si no, en el módulo de reporte de incidentes del micrositio. | [HECHO] https://www.sic.gov.co/content/reporte-de-incidentes-de-seguridad · Circular Única, Título V (actualizada por la Circular 001 del 09/01/2026) |
| Salió dinero | **El banco** de la empresa y el del destinatario | Finanzas | De inmediato | Por teléfono, con los datos de la transferencia | **Propuesto**: no lo verifiqué en una fuente oficial sobre Kali365 |
| Página de phishing alojada en Cloudflare | **Cloudflare**, formulario de abuso, categoría «Phishing» | SOC | Día 0 | https://abuse.cloudflare.com (pide el dominio y el enlace exacto) | [HECHO] https://developers.cloudflare.com/fundamentals/reference/report-abuse/complaint-types/ |
| Bot o canal de Telegram del kit | **Telegram** | SOC | Día 0 | Botón «Report» en la app o correo a abuse@telegram.org con el enlace t.me | [HECHO] https://telegram.org/faq |
| Referencia internacional | **FBI IC3**: acepta denuncias de personas fuera de EE. UU. | Víctima | Opcional | https://www.ic3.gov | [HECHO] https://www.ic3.gov/Home/FAQ |
| Inteligencia compartida contra el phishing | **APWG** | SOC | Opcional | Reenviar el correo como adjunto a reportphishing@apwg.org | [HECHO] https://apwg.org/reportphishing/ |

**Notas:**
- **[HECHO]** La «Agencia Nacional de Seguridad Digital y Asuntos Espaciales» **no existe**. Era el proyecto de ley 023/2023C, hoy archivado (camara.gov.co).
- **[HECHO]** El CSIRT de Gobierno (MinTIC) recibe reportes **de entidades públicas** (Resolución MinTIC 500 de 2021). No es el canal de una empresa privada.
- **[HECHO]** No encontré una norma de 2024 a 2026 que obligue en general a las empresas privadas a reportar incidentes cibernéticos al ColCERT. La obligación verificada es la de **datos personales ante la SIC**. Las entidades del sector financiero tienen además reglas de la Superintendencia Financiera.

### 8.3 Marco legal: responsabilidad penal del delincuente y deberes de la empresa víctima (Colombia)

> **Esto no es asesoría legal.** Cito el texto oficial de las normas. Si un artículo **aplica** a un hecho concreto, lo decide la Fiscalía y un juez; por eso la columna «Por qué podría aplicar» es una **[HIPÓTESIS]** de analista.

**a) Delitos que podrían encuadrar la conducta** (Ley 1273 del 5 de enero de 2009, que adicionó el Título VII bis del Código Penal; texto en secretariasenado.gov.co):

| Artículo | Nombre exacto | Pena (texto oficial) | Por qué podría aplicar [HIPÓTESIS] | A quién |
|---|---|---|---|---|
| 269A | Acceso abusivo a un sistema informático | 48 a 96 meses de prisión y multa de 100 a 1.000 SMLMV | Entrar al buzón y a M365 con el token robado, sin autorización del titular. | Afiliado que usa el token, y el que lo recibe compartido |
| 269C | Interceptación de datos informáticos | 36 a 72 meses | Captura de cookies y tokens en tránsito en el modo AiTM. | Afiliado (rama B) |
| 269E | Uso de software malicioso | 48 a 96 meses y 100 a 1.000 SMLMV | El texto castiga a quien «produzca, trafique, adquiera, distribuya, venda, envíe, introduzca o extraiga del territorio nacional software malicioso». Si el kit encaja como «software malicioso» es una cuestión jurídica. | Autor (produce), revendedor (trafica, vende), afiliado (adquiere) |
| 269F | Violación de datos personales | 48 a 96 meses y 100 a 1.000 SMLMV | Obtener, compilar, intercambiar o vender datos personales: contactos, correos y tokens compartidos entre afiliados. | Afiliado y quien recibe los datos |
| 269G | Suplantación de sitios web para capturar datos personales | 48 a 96 meses y 100 a 1.000 SMLMV | El texto incluye a quien «diseñe, desarrolle, trafique, venda, ejecute, programe o envíe páginas electrónicas, enlaces o ventanas emergentes». Calza con los tres niveles del PhaaS. Según el texto, la pena aumenta si se recluta a víctimas en la cadena del delito. | Autor (diseña, programa), revendedor (trafica, vende), afiliado (envía, ejecuta) |
| 269H | Circunstancias de agravación punitiva | Aumento de la mitad a las tres cuartas partes | Entre sus ocho causales están el provecho propio o ajeno y el uso de un tercero de buena fe. | Todos, según el caso |
| 269I | Hurto por medios informáticos y semejantes | Penas del art. 240 | El texto incluye el hurto cometido «suplantando a un usuario ante los sistemas de autenticación y autorización»: es lo que hace un token robado. | Afiliado que se apropia de dinero |
| 269J | Transferencia no consentida de activos | 48 a 120 meses y 200 a 1.500 SMLMV | Desviar un pago mediante manipulación informática (BEC). El mismo artículo castiga a quien «fabrique, introduzca, posea o facilite programa de computador» destinado a ese delito o a una estafa. | Afiliado; autor o revendedor que facilita el programa |

**b) Cómo se reparte la responsabilidad entre los tres niveles del PhaaS** (Código Penal, Ley 599 de 2000):
- **Art. 29, «Autores»:** quien realiza la conducta, y los coautores que actúan con acuerdo común y división del trabajo. **[HIPÓTESIS]** El afiliado que envía el correo y usa el token sería autor de los delitos que comete.
- **Art. 30, «Partícipes»:** el determinador y el cómplice. **[HIPÓTESIS]** El autor del kit y los revendedores podrían responder como partícipes de los delitos de los afiliados, además de por sus propias conductas de los arts. 269E, 269G y 269J (producir, vender o facilitar).
- **Art. 340, «Concierto para delinquir»:** concertarse para cometer delitos, con pena aumentada para quien organice, dirija o financie. **[HIPÓTESIS]** Un esquema estable de autor, revendedores y afiliados podría encuadrar aquí.
- **Ley 1928 de 2018:** aprueba el Convenio sobre la Ciberdelincuencia (Budapest, 2001). Según el Consejo de Europa, el convenio rige para Colombia desde el 01/07/2020. Importa porque los operadores, la infraestructura (Cloudflare) y la mensajería (Telegram) están fuera del país: el convenio es la base de la **cooperación internacional** para pedir pruebas.

**c) Deberes y responsabilidad de la empresa víctima** (no es responsabilidad penal: la empresa y su empleado son **víctimas**):
- **[HECHO, Ley 1581 de 2012, art. 17]** Como responsable del tratamiento de datos personales, la empresa debe:
  - «Conservar la información bajo las condiciones de seguridad necesarias para impedir su adulteración, pérdida, consulta, uso o acceso no autorizado o fraudulento» (lit. d).
  - «Informar a la autoridad de protección de datos cuando se presenten violaciones a los códigos de seguridad y existan riesgos en la administración de la información de los Titulares» (lit. n).
- **[HECHO]** El encargado del tratamiento tiene el deber equivalente de informar a la SIC (art. 18, lit. k).
- **[HECHO]** La Circular Única de la SIC fija **15 días hábiles** desde la detección.
- **[HECHO, art. 23]** La SIC puede imponer:
  - Multas de hasta 2.000 SMLMV.
  - Suspensión de actividades.
  - Cierre temporal, o cierre inmediato y definitivo cuando se trata de datos sensibles.

  **[SUPOSICIÓN por verificar]** La Ley 2294 de 2023 (art. 313) ordena calcular en UVB las multas fijadas en salarios mínimos; no verifiqué cómo lo aplica hoy la SIC.
- **[HIPÓTESIS]** Un buzón comprometido con datos de clientes, sin MFA ni controles razonables, podría llevar a la SIC a cuestionar el «deber de seguridad». Por eso los controles de la sección 3 también sirven como **evidencia de diligencia**: la directiva activa, las alertas, la capacitación con asistencia y el informe del incidente.
- **[SUPOSICIÓN]** La responsabilidad civil frente a clientes o proveedores que pierdan dinero por un BEC enviado desde la cuenta de la empresa depende del contrato y del caso. No verifiqué normas específicas: requiere un abogado.

**Fuentes legales (texto oficial, consultado el 05/11/2026):**
- Ley 1273 de 2009. http://www.secretariasenado.gov.co/senado/basedoc/ley_1273_2009.html
- Ley 1581 de 2012. http://www.secretariasenado.gov.co/senado/basedoc/ley_1581_2012.html
- Ley 1928 de 2018. http://www.secretariasenado.gov.co/senado/basedoc/ley_1928_2018.html
- Código Penal (Ley 599 de 2000). http://www.secretariasenado.gov.co/senado/basedoc/ley_0599_2000.html
- Consejo de Europa, tratado 185 (Convenio de Budapest), estado de firmas y ratificaciones. https://www.coe.int/en/web/conventions/full-list?module=signatures-by-treaty&treatynum=185
- SIC, Circular Única, Título V. https://sedeelectronica.sic.gov.co/transparencia/normativa/titulo-v-actualizado-el-9-de-enero-de-2026

---

## 9. Validación: cuatro preguntas para mí

Son las preguntas de cierre del ejercicio: se responden sin mirar una respuesta modelo, y luego se evalúa el razonamiento.

1. **RECONOCE.** En los registros de Entra ID, ¿qué combinación de señales te haría sospechar de phishing por código de dispositivo, y no del inicio de sesión normal de una sala de Teams?
2. **APLICA.** Una empresa de 40 personas con Microsoft 365, dos salas de Teams Android y un técnico que usa Azure CLI te pide **una sola** medida para esta semana. ¿Cuál configuras, con qué excepciones y cómo compruebas que no rompiste nada?
3. **ANALIZA.** Si el afiliado usa el modo «cookie link» (AiTM) en lugar del código, ¿qué controles de este reporte siguen sirviendo y cuáles no? ¿Por qué FIDO2 cambia la respuesta en un modo y no en el otro?
4. **TRANSFIERE.** Tu empresa conecta un agente de IA (por ejemplo, mediante un servidor MCP) al correo, con permisos delegados de una persona. Con la lección de Kali365: ¿cuál es el activo crítico, qué pasa si alguien lo roba, y qué tres controles pondrías?

## 10. Autoevaluación: ¿criterio de IA o ciberseguridad general?

- **Honestamente, la mayor parte del caso es seguridad de identidad clásica.** El ataque, la detección y la contención no necesitan IA para existir, y lo marqué así.
- **El aporte específico de IA está en cuatro lugares:**
  1. Tratar el **token delegado** como el activo que comparten personas y agentes.
  2. Añadir la auditoría de la IA como fuente de registro **obligatoria** del SOC.
  3. Reconocer que el **contenido sembrado sobrevive a la revocación del token**: una persistencia nueva que el playbook clásico no limpia.
  4. Llevar a la contención y a la recuperación la revisión de consentimientos y permisos de agentes.
- **Lo que me falta para afirmarlo:** evidencia de un caso real en el que un kit PhaaS use la IA de la víctima. Hasta tenerla, la variante IA es **hipótesis**, factible según AML.CS0026 y AML.CS0059.

---

## Fuentes (consultadas el 05/11/2026, salvo que se indique otra fecha)

**Principales**
1. Arctic Wolf Labs, «Token Bingo: Don't Let Your Code be the Winner», 24/04/2026. https://arcticwolf.com/resources/blog/token-bingo-dont-let-your-code-be-the-winner/
2. CISA, NSA, FBI y MS-ISAC, «Phishing Guidance: Stopping the Attack Cycle at Phase One», octubre de 2023 (TLP:CLEAR). https://www.cisa.gov/sites/default/files/2025-03/Phishing%20Guidance%20-%20Stopping%20the%20Attack%20Cycle%20at%20Phase%20One%20508.pdf

**Oficiales de apoyo**

3. FBI IC3, PSA I-052126-PSA, «Kali365 Phishing-as-a-Service Kit Hijacks Microsoft 365 Access Tokens», 21/05/2026. https://www.ic3.gov/PSA/2026/PSA260521
4. MITRE ATT&CK, «Kali365», S9044 (v1.0, creada el 30/07/2026; ATT&CK v20.1). https://attack.mitre.org/software/S9044/
5. MITRE ATLAS, datos oficiales, release 2026.10 (28/10/2026). https://atlas.mitre.org/ y https://github.com/mitre-atlas/atlas-data. Casos: AML.CS0026 (https://atlas.mitre.org/studies/AML.CS0026) y AML.CS0059 (https://atlas.mitre.org/studies/AML.CS0059).
6. Microsoft Learn:
   - «Authentication flows as a condition in Conditional Access policy». https://learn.microsoft.com/en-us/entra/identity/conditional-access/concept-authentication-flows
   - «Block authentication flows with Conditional Access policy». https://learn.microsoft.com/en-us/entra/identity/conditional-access/policy-block-authentication-flows
   - Teams y device code flow. https://learn.microsoft.com/en-us/entra/identity/conditional-access/policy-teams-devices-device-code-flow
   - Transferencia de autenticación. https://learn.microsoft.com/en-us/entra/identity/conditional-access/concept-authentication-transfer
   - Directivas administradas. https://learn.microsoft.com/en-us/entra/identity/conditional-access/managed-policies
   - Valores predeterminados de seguridad. https://learn.microsoft.com/en-us/entra/fundamentals/security-defaults
7. Microsoft Learn, respuesta:
   - «Revoke user access». https://learn.microsoft.com/en-us/entra/identity/users/users-revoke-access
   - revokeSignInSessions. https://learn.microsoft.com/en-us/graph/api/user-revokesigninsessions?view=graph-rest-1.0
   - Protección de tokens. https://learn.microsoft.com/en-us/entra/identity/conditional-access/concept-token-protection
   - CAE. https://learn.microsoft.com/en-us/entra/identity/conditional-access/concept-continuous-access-evaluation
8. Microsoft Learn, Copilot:
   - Privacidad y permisos. https://learn.microsoft.com/en-us/copilot/microsoft-365/microsoft-365-copilot-privacy
   - Auditoría *CopilotInteraction*. https://learn.microsoft.com/en-us/purview/audit-copilot
   - APIs de Copilot. https://learn.microsoft.com/en-us/microsoft-365-copilot/extensibility/copilot-apis-overview
9. Microsoft Learn, herramientas con código de dispositivo:
   - Azure CLI. https://learn.microsoft.com/en-us/cli/azure/authenticate-azure-cli-interactively
   - Azure PowerShell. https://learn.microsoft.com/en-us/powershell/azure/authenticate-interactive
   - Microsoft Graph PowerShell. https://learn.microsoft.com/en-us/powershell/microsoftgraph/authentication-commands
   - Políticas soportadas en Teams Rooms. https://learn.microsoft.com/en-us/microsoftteams/rooms/supported-ca-and-compliance-policies
10. Microsoft Security Blog, «Inside an AI-enabled device code phishing campaign», 06/04/2026. https://www.microsoft.com/en-us/security/blog/2026/04/06/ai-enabled-device-code-phishing-campaign/
11. IETF, RFC 8628, «OAuth 2.0 Device Authorization Grant», §5.4. https://datatracker.ietf.org/doc/html/rfc8628#section-5.4
12. Model Context Protocol, especificación de autorización (2026-07-28). https://modelcontextprotocol.io/specification/latest/basic/authorization
13. CVE-2025-32711:
    - MSRC. https://msrc.microsoft.com/update-guide/vulnerability/CVE-2025-32711
    - NVD (API). https://services.nvd.nist.gov/rest/json/cves/2.0?cveId=CVE-2025-32711

**Empresas de seguridad** (verificadas el 04/11/2026 en el guion de la charla)

14. Huntress, «Hook, Line, and Token…», 11/06/2026. https://www.huntress.com/blog/kali365-device-code-phishing-kit
15. SpyCloud, «Kali365: Anatomy of a Microsoft 365 Phishing-as-a-Service Kit…», 11/06/2026. https://spycloud.com/blog/kali365-anatomy-of-a-microsoft365-phishing-as-a-service-kit/
16. Barracuda, «Kali365 and why stealing passwords is no longer enough», actualizado el 02/09/2026. https://blog.barracuda.com/2026/09/02/kali365-phishing-microsoft-365-authentication-abuse

**Canales y normas de Colombia, y plataformas** (todos verificados el 05/11/2026)

17. ColCERT:
    - Reportar un incidente. https://www.colcert.gov.co/800/w3-article-198656.html
    - Reportar phishing. https://www.colcert.gov.co/800/w3-propertyvalue-386766.html
18. Policía Nacional:
    - Ciberseguridad. https://www.policia.gov.co/ciberseguridad
    - ¡ADenunciar!. https://www.policia.gov.co/sistema-nacional-denuncia-virtual-adenunciar
    - CAI Virtual. https://caivirtual.policia.gov.co
19. Fiscalía, «Dónde y cómo denunciar». https://www.fiscalia.gov.co/servicios-de-informacion-al-ciudadano/donde-y-como-denunciar/
20. SIC, «Reporte de incidentes de seguridad». https://www.sic.gov.co/content/reporte-de-incidentes-de-seguridad
21. Cámara de Representantes, proyecto 023/2023C (archivado). https://www.camara.gov.co/agencia-nacional-de-seguridad-digital-y-asuntos-espaciales-1439/
22. Plataformas:
    - Microsoft Learn, reporte desde Outlook. https://learn.microsoft.com/en-us/defender-office-365/submissions-outlook-report-messages
    - MSRC. https://msrc.microsoft.com/report/
    - Cloudflare, tipos de queja. https://developers.cloudflare.com/fundamentals/reference/report-abuse/complaint-types/
    - Telegram, preguntas frecuentes. https://telegram.org/faq
    - FBI IC3, preguntas frecuentes. https://www.ic3.gov/Home/FAQ
    - APWG. https://apwg.org/reportphishing/
23. Las leyes del apartado 8.3 (enlaces junto a la sección).

## Puntos por verificar

1. **Final del artículo de Arctic Wolf.** Solo lo leí por resumen. No uso el hash del certificado ni los hashes de las páginas que, según ese resumen, aparecen en la tabla de IOC. Los dominios `kali365[.]xyz` y `api.kali365[.]xyz` vienen de esa tabla: los confirman los apuntes de clase (08), pero en esta sesión no los vi en el texto literal. `v2.kali365[.]xyz`, las tres IP y el User-Agent sí los vi en el texto literal.
2. **FIDO2 frente al código de dispositivo.** Que no lo frene es una inferencia técnica mía; ninguna fuente lo dice de forma literal.
3. **La condición de ubicación del acceso condicional en el device code flow.** No encontré documentado qué IP evalúa.
4. **La protección de tokens frente a Kali365.** Ninguna fuente dice que bloquee un token obtenido por código de dispositivo. Su página tampoco indica la licencia.
5. **La directiva administrada «Block device code flow».** Microsoft Learn confirma que existe, pero no da fecha ni alcance. El blog de Tech Community (20/02/2025) habla de 45 días en modo informe; Learn dice ahora 30 días.
6. **La variante IA.** No hay evidencia pública de que Kali365 haya consultado asistentes de IA de las víctimas. Es **hipótesis**.
7. **Las cifras de SpyCloud** (500+ afiliados): vienen de un solo proveedor.
8. **Los IDs candidatos** (T1098.005, T1114.002, T1114.003, T1534, T1078.004, T1583.006, T1608.005, T1657, T1684.001) existen en ATT&CK, pero MITRE no los asigna a Kali365: son mapeo mío.
9. **Las URL de atlas.mitre.org.** Las verifiqué con el archivo de datos oficial que carga el sitio; no las vi renderizadas.
10. **Este reporte no es asesoría legal.** Las normas de la sección 8.3 se citan del texto oficial; su aplicación a un caso concreto requiere un abogado.

---
*Trabajo hecho sobre fuentes públicas, sin ejecutar ningún ataque, como exige el Manifiesto de Ciberdefensa Responsable.*
