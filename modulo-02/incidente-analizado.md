# Incidente analizado: Kali365, phishing como servicio contra Microsoft 365

> **Módulo 2 · ¿Cómo piensa un adversario?** · AI Cyber Defense Academy (Campuslands × ETHOS IA)
> **Autor:** Jhon Fredy León García · **Fecha:** 05/11/2026 · **Caso de la clase 5**
> **Enfoque:** defensivo. Describo el comportamiento del adversario solo lo necesario para reconocerlo, detectarlo y romper la cadena. No hay instrucciones operativas.
> **Etiquetas:** **[HECHO]** lo confirma una fuente · **[HIPÓTESIS]** es plausible, pero hay que verificarla · **[SUPOSICIÓN]** es una lectura mía o una buena práctica general.

Piezas relacionadas: [línea de tiempo](linea-tiempo.md) · [mapa del ecosistema](mapa-ecosistema.png) ([editable en Excalidraw](mapa-ecosistema.excalidraw)) · [brief de 150 palabras](brief-150-palabras.md).

---

## 1. Ficha del caso

| Campo | Detalle |
|---|---|
| Qué es | Kit de **phishing como servicio** (PhaaS) que se alquila por suscripción para robar el acceso a cuentas de Microsoft 365. [HECHO: FBI, MITRE] |
| Otros nombres | Octopi365 y Freedom365 [HECHO: MITRE]. Tras la alerta del FBI, sus operadores pasaron a la marca «Green Octopus». [HECHO, de un solo proveedor: SpyCloud] |
| Desde cuándo | El FBI y MITRE lo ven desde **abril de 2026** [HECHO]. SpyCloud fecha la primera actividad el 15/02/2026 [HECHO, de un solo proveedor]. |
| Objetivo | Cuentas de **Microsoft 365**: correo, Teams y OneDrive. Arctic Wolf documentó después páginas falsas de Okta, Xerox DocuShare y otros servicios. [HECHO: FBI, Arctic Wolf] |
| A quién afectó | Organizaciones de manufactura, educación, gobierno, seguros, finanzas y salud en Norteamérica y EMEA. [HECHO: Arctic Wolf] |
| Técnica principal | Phishing con **código de dispositivo** (*device code phishing*): la víctima escribe un código del atacante en la página **real** de Microsoft y, sin saberlo, le autoriza el acceso. [HECHO: FBI, Arctic Wolf, MITRE] |
| Variantes | **Intermediario** (AiTM, modo «Cookie Link»), que roba cookies de sesión, y **copiar y pegar** al estilo ClickFix. [HECHO: Arctic Wolf, MITRE] |
| Catalogación | MITRE ATT&CK lo registra como software **S9044** desde el 30/07/2026. [HECHO] |
| Alerta oficial | FBI IC3, **PSA I-052126-PSA**, 21/05/2026. [HECHO] |

## 2. Por qué importa (Golden Circle del defensor)

- **Qué protegemos:** la **identidad digital** de las personas de una empresa, es decir, su sesión de Microsoft 365. Detrás de ella están el correo, los archivos, los contactos y las conversaciones de pagos.
- **A quién afecta:**
  - Al empleado engañado.
  - A sus contactos, que reciben phishing desde una cuenta real.
  - A los clientes y proveedores, que pueden pagarle a una cuenta falsa.
  - A la empresa, que pierde dinero, datos y confianza.
- **Consecuencia humana y empresarial:** la víctima no siente que la «hackearon», porque usó la página real y su verificación en dos pasos de siempre. Eso retrasa el reporte, y cada minuto de silencio le da ventaja al atacante. [SUPOSICIÓN: lectura propia a partir del mecanismo que describen el FBI y Arctic Wolf]

## 3. Hechos, hipótesis y suposiciones

| Afirmación | Tipo | Fuente |
|---|---|---|
| El atacante pide un código de dispositivo legítimo y la víctima lo escribe en `microsoft.com/devicelogin`. | [HECHO] | FBI, Arctic Wolf |
| La víctima completa su MFA en la infraestructura real de Microsoft. El atacante nunca ve la contraseña ni el código de MFA. | [HECHO] | Arctic Wolf |
| El panel captura los tokens OAuth de acceso y de actualización, y los afiliados pueden **compartirlos** entre sí. | [HECHO] | Arctic Wolf, FBI |
| El kit se vende por suscripción: unos **USD 250 por 30 días**. | [HECHO] | Arctic Wolf (lista de precios del panel) y SpyCloud |
| Tiene tres niveles: autor, revendedor y afiliado. | [HECHO] | Arctic Wolf |
| Genera señuelos en 14 idiomas, entre ellos el español, y plantillas de correo **asistidas por IA**. | [HECHO] | Arctic Wolf |
| Tiene un módulo de IA que califica correos y redacta respuestas para fraude **BEC**. | [HECHO, según el código del kit] | MITRE, Huntress. No se sabe cuánto lo usan los afiliados. |
| Crea reglas de buzón que esconden o borran alertas de seguridad. | [HECHO] | MITRE, Arctic Wolf, SpyCloud |
| En algunos casos registra un dispositivo adicional en la cuenta de la víctima. | [HECHO] | Arctic Wolf |
| Huntress midió **42 segundos** entre el clic y el acceso persistente. | [HECHO] | Huntress |
| Microsoft no lo trata como una falla para parchear: recomienda **bloquear el flujo de código de dispositivo** donde no se necesite. | [HECHO] | Microsoft Learn, FBI |
| Tras la alerta del FBI, la marca se «retiró», pero la captura de cuentas siguió. | [HECHO, de un solo proveedor] | SpyCloud |
| Hubo «muchos ataques» de Kali365 en los meses previos a septiembre de 2026. | [HECHO] | Barracuda (02/09/2026) |
| Los accesos robados se **revenden** a otros criminales, fuera del grupo de afiliados. | [HIPÓTESIS] | Lo sugiere la diapositiva 7 de la clase («reventa»). No lo vi documentado. |
| Hay víctimas en Colombia o en Latinoamérica. | [HIPÓTESIS] | Ninguna fuente revisada lo menciona. Los señuelos en español lo hacen posible. |
| La técnica seguirá circulando con otras marcas. | [SUPOSICIÓN] | Cambio de marca documentado por SpyCloud; Microsoft documentó otro kit con la misma técnica (EvilTokens). |

## 4. Cadena de ataque de alto nivel

Uso las cinco etapas de la clase: **Acceso → Operación → Datos/Control → Monetización → Impacto**. Las técnicas son las que **MITRE ATT&CK asigna a Kali365 (S9044)**, consultado el 05/11/2026. Lo que MITRE no mapea va marcado como *propuesta*.

| Etapa | Qué pasa (sin detalle operativo) | Objeto comprometido | Evidencia observable | Dónde se ve (registro) | Técnica (ATT&CK) |
|---|---|---|---|---|---|
| **1. Acceso** | Llega un correo o un adjunto que imita SharePoint, OneDrive, Teams, DocuSign, Adobe Sign, un buzón de voz o una factura, y lleva a una página que pide escribir un código. | La confianza del usuario | Correos con asuntos de urgencia; enlaces a páginas alojadas en servicios en la nube; reportes de usuarios | Pasarela de correo, reportes de phishing | *Phishing* con adjunto o enlace (T1566.001/.002) · *User Execution*: enlace malicioso y copiar y pegar malicioso (T1204.001/.004) · *Generate Content: Written Content* (T1683.001) |
| **2. Operación** | Las páginas del actor, alojadas en servicios web, generan el código en el momento y esperan a que la víctima autorice. | El flujo de autenticación | Inicios de sesión con protocolo de **código de dispositivo** que nadie esperaba; tráfico a dominios del kit (`kali365[.]xyz`) | Registros de inicio de sesión de Entra ID; proxy y DNS | *Web Service* (T1102) · *Proxy* (T1090) · *JavaScript* (T1059.007) · *Adversary-in-the-Middle* (T1557) |
| **3. Datos / control** | El panel recibe los **tokens OAuth** o las **cookies de sesión** y entra al correo, Teams y OneDrive sin contraseña ni más MFA. | La **sesión** y los tokens delegados | Accesos a Graph con el agente de usuario `kali365-live/1.0.0`; dispositivos nuevos registrados; inicios desde IP o nubes inusuales | Registros de Entra ID (inicios y auditoría); actividad de Graph | *Steal Application Access Token* (T1528) · *Steal Web Session Cookie* (T1539) · *Browser Session Hijacking* (T1185) · *Account Discovery: Email Account* (T1087.003) · *Application Access Token* (T1550.001) · Registro de dispositivo (*propuesta: T1098.005*; lo documenta Arctic Wolf, pero MITRE no lo mapea) |
| **4. Monetización** | Busca conversaciones de pagos para fraude BEC (con ayuda de IA), envía phishing a los contactos, busca claves y frases semilla en los correos y esconde las alertas con reglas de buzón. | El buzón y la reputación de la cuenta | Reglas nuevas que mueven o borran correos con «spam», «phish», «click», «link» o «SharePoint»; picos de envío; reenvíos externos | Auditoría del buzón (reglas, reenvíos, conectores) | *Email Hiding Rules* (T1564.008) · *Credentials In Files* (T1552.001) · *Generate Content* (T1683.001) · Phishing interno (*propuesta: T1534*) |
| **5. Impacto** | Pagos desviados, datos expuestos, clientes engañados y pérdida de confianza. | Dinero, datos y confianza | Reclamos de clientes o proveedores; transferencias que no se reconocen | Banco, finanzas, mesa de ayuda | Robo financiero (*propuesta: T1657*; MITRE no mapea el impacto de S9044) |

**Bifurcación:** en el **modo código** se secuestra la *autorización*: la víctima aprueba en la página real. En el **modo cookie** (AiTM) se secuestra una *sesión ya iniciada*: la víctima inicia sesión a través de un proxy del atacante. [HECHO: Arctic Wolf]

**Condición agravante:** si la cuenta comprometida tiene privilegios de administración, el kit puede hacer cambios administrativos, como crear conectores de correo o cambiar reglas de flujo de correo. [HECHO: Arctic Wolf, MITRE]

## 5. Matriz de oportunidades defensivas

| Etapa | Prevenir | Detectar | Contener | Recuperar | Origen del control |
|---|---|---|---|---|---|
| **Acceso** | Regla de oro: *«Nunca escribas ni pegues un código que te llegó por correo, chat o una página web»*. Capacitación con ejemplos reales. Filtros y validación de remitentes. | Botón de «reportar phishing» y reporte en minutos | Bloquear el remitente y los dominios del señuelo | Usar los casos reportados en la próxima capacitación | Capacitación: Arctic Wolf y Microsoft · Regla de oro: **propuesta** basada en el FBI y Microsoft |
| **Operación** | **Bloquear el flujo de código de dispositivo** con Acceso Condicional donde no se use; si se necesita (salas, señalización), restringirlo por red, plataforma o grupo. Bloquear también la transferencia de autenticación. | Alertas por inicios con código de dispositivo; **políticas de riesgo de inicio de sesión** (Entra ID Protection) | Bloquear la IP o el dominio del panel | Revisar quién usaba el flujo de forma legítima y documentar las excepciones | Arctic Wolf, FBI, Microsoft Learn |
| **Datos / control** | MFA resistente a phishing (FIDO2 o passkeys) para gerencia, finanzas y administradores. Frena la variante AiTM, pero **no** el truco del código. | Agente de usuario `kali365-live/1.0.0`; dispositivos nuevos; inicios desde nubes poco usuales | **Revocar sesiones y tokens** y deshabilitar la cuenta un tiempo, porque los tokens de acceso pueden seguir activos hasta 1 hora. Quitar los dispositivos registrados. | Rehacer la MFA y la contraseña **después** de revocar | Arctic Wolf, MITRE, Microsoft · Alcance de FIDO2: **propuesta** (lectura técnica propia) |
| **Monetización** | Exigir confirmación por teléfono de todo cambio de cuenta bancaria | Alertas por **reglas de buzón** y reenvíos nuevos; picos de envío por cuenta | Borrar reglas, reenvíos y conectores maliciosos; congelar pagos en curso | Avisar por teléfono a clientes y proveedores | Reglas: Arctic Wolf, MITRE, Barracuda · Picos de envío: Huntress · Confirmación por teléfono: **propuesta** (buena práctica contra BEC) |
| **Impacto** | Respaldo de la gerencia: el reporte rápido no se castiga | Conciliación de pagos; reclamos de clientes | Contactar al banco de inmediato | Documentar, denunciar y ajustar los controles | FBI (guardar evidencia) · Alcaldía de Bogotá (canales de denuncia) · Resto: **propuesta** |
| **Variante IA** (asistente o agente con token delegado) | **Mínimo privilegio** en los permisos (*scopes*) de copilotos, agentes y servidores MCP; inventario de apps y agentes con acceso; revisar los consentimientos OAuth | Monitorear el uso del asistente desde sesiones anómalas (consultas atípicas, volumen inusual) | Aislar a los agentes en una sandbox con red y credenciales limitadas, como NVIDIA OpenShell, que se mencionó en la clase; revocar los tokens del agente | Tratar el contenido externo como **no confiable**; exigir **aprobación humana** para acciones sensibles | **Propuesta** del analista, basada en los principios del curso |

## 6. ¿Dónde rompemos la cadena?

Priorizado por impacto, urgencia y esfuerzo:

| # | Punto de ruptura | Etapa que corta | Impacto | Urgencia | Esfuerzo | Por qué |
|---|---|---|---|---|---|---|
| 1 | **Bloquear el flujo de código de dispositivo** donde no se use, con excepciones auditadas | Operación → Datos/Control | Alto | Alta | Bajo-medio | Quita la puerta principal del kit. Lo recomiendan el FBI, Microsoft y Arctic Wolf. [HECHO] |
| 2 | **Regla de oro y reporte en minutos** | Acceso | Alto | Alta | Bajo | El acceso puede quedar listo en 42 segundos [HECHO: Huntress]. Solo una persona que se detiene, o que reporta rápido, gana esa carrera. |
| 3 | **Detección de identidad y respuesta rápida**: alertas de inicio por código, de reglas de buzón y de dispositivos nuevos, más el procedimiento de revocar sesiones | Datos/Control → Monetización | Alto | Media | Medio | Si alguien cae, evita que el acceso se convierta en dinero. [Controles de Arctic Wolf, Microsoft y Barracuda] |

**¿Por qué el MFA tradicional no basta?** La víctima completa ella misma su MFA en la página legítima de Microsoft. El atacante recibe tokens emitidos **después** de esa verificación y puede entrar sin contraseña ni más MFA [HECHO: FBI, Arctic Wolf]. El MFA sigue siendo indispensable contra el robo de contraseñas, pero aquí no frena el engaño. Contra este kit lo que funciona es el **bloqueo del flujo** más el **criterio humano**.

## 7. El ecosistema criminal detrás del caso

Kali365 es un ejemplo de **cibercrimen como servicio** (CaaS):

| Pieza del ecosistema | En Kali365 | Modelo «como servicio» |
|---|---|---|
| Quien crea la capacidad | Autor del kit (nivel administrador) [HECHO: Arctic Wolf] | PhaaS |
| Quien distribuye | Revendedores (nivel agente) [HECHO: Arctic Wolf] | PhaaS |
| Quien ataca | Afiliados que pagan la suscripción y no necesitan experiencia técnica avanzada [HECHO: FBI, Arctic Wolf] | PhaaS |
| Servicios de apoyo | Telegram (venta, alertas y recuperación de cuentas), pagos en criptomonedas sin verificación de identidad, Cloudflare Workers (páginas desechables) e IA (señuelos y módulo BEC) [HECHO: Arctic Wolf, MITRE, Huntress] | Infraestructura que se alquila |
| Mercado del botín | Tokens compartidos entre afiliados [HECHO: Arctic Wolf]; reventa de accesos a terceros [HIPÓTESIS] | Acceso como servicio (AaaS) e intermediario de acceso inicial (IAB) |
| Cómo se convierte en dinero | Fraude BEC, phishing a contactos y robo de claves o frases semilla [HECHO: MITRE, Huntress] | — |

**Lectura propia [SUPOSICIÓN]:** el producto que se vende no es un virus. Es un **servicio de engaño listo para usar**. Por eso la defensa no puede depender solo de bloquear un archivo o un dominio, que cambian rápido. Tiene que cortar **la técnica**, es decir, el flujo de código de dispositivo, y **el comportamiento** de las personas.

## 8. Puente con la seguridad de la IA

- **La IA como acelerador del atacante [HECHO]:**
  - Plantillas de correo asistidas por IA, en 14 idiomas y a escala (Arctic Wolf).
  - Módulo de IA para fraude BEC, según el código del kit (MITRE, Huntress).
  - Microsoft documentó en abril de 2026 otra campaña de código de dispositivo habilitada con IA.
- **Los tokens OAuth como activo de IA [HIPÓTESIS]:**
  - Los copilotos, los agentes, los conectores y los servidores MCP entran al correo, a los archivos y a las API con tokens delegados, como los que roba este kit.
  - Un token robado con permisos amplios podría servir para pedirle al asistente corporativo que resuma información sensible.
  - También podría servir para dejar contenido trampa que después lea un agente (**inyección indirecta de prompts**).
- **Efecto colateral del control [HIPÓTESIS]:** algunas herramientas de IA y de línea de comandos sin navegador usan el flujo de código de dispositivo de forma legítima. Antes de bloquearlo, hay que **auditar quién lo usa**.
- **Qué cambia en la detección [SUPOSICIÓN]:** si la identidad comprometida tiene un asistente de IA conectado, el atacante puede reunir información en minutos y con menos ruido. Hay que vigilar también el **uso del asistente**, no solo los inicios de sesión.

## 9. Dónde reportar y marco legal (Colombia)

- **Denunciar:** CAI Virtual de la Policía (https://caivirtual.policia.gov.co) y el portal A Denunciar (https://adenunciar.policia.gov.co). [HECHO: Alcaldía de Bogotá, 28/05/2024]
- **Si hubo fraude:** avisar al banco de inmediato. [SUPOSICIÓN: buena práctica]
- **Evidencia que pide el FBI:** el correo con sus encabezados, los inicios sospechosos (hora, IP y ubicación) y los dispositivos o sesiones no autorizados. [HECHO: FBI]
- **Marco legal [SUPOSICIÓN: lectura propia, no es asesoría legal]:**
  - La **Ley 1273 de 2009** tipifica los delitos informáticos en Colombia. Entre los que podrían aplicar a un caso así están el acceso abusivo a un sistema informático, la suplantación de sitios web para capturar datos personales, la violación de datos personales y la transferencia no consentida de activos.
  - Si se exponen datos personales, la empresa debe revisar sus deberes según la **Ley 1581 de 2012** de protección de datos.

  Hay que verificarlo con un abogado.

## 10. Lo que no sabemos

1. Cuántas víctimas hubo en total y si hay casos en Colombia.
2. Cuántos afiliados usaron de verdad el módulo de IA y con qué resultados.
3. Si la operación sigue hoy con otra marca. Barracuda reportó ataques hasta septiembre de 2026 y SpyCloud vio que la actividad continuó tras el «retiro».
4. Quiénes son los operadores. Ninguna fuente pública los identifica.
5. Las cifras de SpyCloud (más de 500 afiliados e ingresos estimados). Son de un solo proveedor.

## Fuentes

1. **FBI, Internet Crime Complaint Center (IC3), PSA I-052126-PSA, 21/05/2026.** «Kali365 Phishing-as-a-Service Kit Hijacks Microsoft 365 Access Tokens» («El kit de phishing como servicio Kali365 secuestra tokens de acceso de Microsoft 365»). https://www.ic3.gov/PSA/2026/PSA260521
2. **Arctic Wolf Labs, 24/04/2026.** «Token Bingo: Don't Let Your Code Be the Winner» («Bingo de tokens: que tu código no sea el ganador»). https://arcticwolf.com/resources/blog/token-bingo-dont-let-your-code-be-the-winner/ (leído el 05/11/2026)
3. **Arctic Wolf Labs, 02/06/2026.** «From Token Bingo to MAX Takeover…» («Del bingo de tokens al robo de cuentas de MAX…»). https://arcticwolf.com/resources/blog/kali365-expands-into-aws-microsoft-okta-xerox-max-messenger/
4. **MITRE ATT&CK, software S9044, «Kali365»**, v1.0 (30/07/2026). https://attack.mitre.org/software/S9044/ (consultado el 05/11/2026)
5. **Huntress, 11/06/2026.** «Hook, Line, and Token: Anatomy of the Kali365 / Octopi365 Phishing-as-a-Service Kit» («Anzuelo, sedal y token: anatomía del kit de phishing como servicio Kali365 / Octopi365»). https://www.huntress.com/blog/kali365-device-code-phishing-kit
6. **SpyCloud, 11/06/2026.** «Kali365: Anatomy of a Microsoft 365 Phishing-as-a-Service Kit» («Kali365: anatomía de un kit de phishing como servicio para Microsoft 365»). https://spycloud.com/blog/kali365-anatomy-of-a-microsoft365-phishing-as-a-service-kit/
7. **Microsoft Security Blog, 06/04/2026.** «Inside an AI-enabled device code phishing campaign» («Dentro de una campaña de phishing de código de dispositivo habilitada con IA»). https://www.microsoft.com/en-us/security/blog/2026/04/06/ai-enabled-device-code-phishing-campaign/
8. **Microsoft Learn, 25/03/2026.** «Authentication flows as a condition in Conditional Access policy» («Flujos de autenticación como condición en una directiva de acceso condicional»). https://learn.microsoft.com/en-us/entra/identity/conditional-access/concept-authentication-flows
9. **Barracuda, 02/09/2026.** «Kali365 and why stealing passwords is no longer enough» («Kali365 y por qué robar contraseñas ya no es suficiente»). https://blog.barracuda.com/2026/09/02/kali365-phishing-microsoft-365-authentication-abuse
10. **Alcaldía de Bogotá, 28/05/2024.** «¿Cómo denunciar delitos informáticos ante las autoridades?». https://bogota.gov.co/mi-ciudad/seguridad/como-denunciar-delitos-informaticos-en-bogota-ante-las-autoridades
11. **Material del curso:** presentación «Módulo 2, Sesión 2» (05/11/2026) y el video «Kali365 Phishing and Microsoft 365 Token Theft» («Phishing de Kali365 y robo de tokens de Microsoft 365»), de Juan Romero (SOCFortress): https://www.youtube.com/watch?v=ELjcA_qT9mA
12. **NVIDIA OpenShell, documentación** (QR «Investigación» de la clase). https://docs.nvidia.com/openshell/latest/about/overview

**Pendiente de leer:** la guía de la CISA *«Phishing Guidance: Stopping the Attack Cycle at Phase One»* («Guía sobre phishing: detener el ciclo de ataque en la fase uno»), que cita el prompt del ejercicio de clase.
