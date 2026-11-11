# Kali365, explicado sin tecnicismos: cómo roban una cuenta sin la contraseña y qué hacer

**Autor:** Jhon Fredy León García · AI Cyber Defense Academy (Campuslands × ETHOS IA Consultores) · 05/11/2026
**Para:** empresarios, gerentes y todo el personal que usa correo de la empresa. La versión técnica está en [`reporte-tecnico.md`](reporte-tecnico.md).

> **Cómo leer las etiquetas:**
> - **Hecho:** lo dice una fuente pública (Arctic Wolf, el FBI, Microsoft, CISA o MITRE).
> - **Hipótesis:** algo posible que todavía no está confirmado.
> - **Propuesta:** una recomendación mía, que no viene de las fuentes.

---

## 1. Qué pasó, en 30 segundos

- **Hecho.** Desde abril de 2026 circula un «kit» criminal llamado **Kali365** que roba cuentas de **Microsoft 365**, el paquete de correo Outlook, Teams, OneDrive y SharePoint que usan muchas empresas. Lo documentaron la empresa de seguridad Arctic Wolf (24/04/2026) y el FBI (21/05/2026).
- **Hecho.** El kit se **alquila**, como cualquier programa por suscripción: unos **US$250 al mes**.
- **Hecho.** La víctima **no entrega su contraseña**. Entrega algo que vale más: su **sesión**, el «pase» que mantiene la cuenta abierta.

## 2. Cómo engaña: el televisor del ladrón

**El uso normal.** Para activar una app de streaming en un televisor, la pantalla muestra un código. Usted lo escribe en su celular y el televisor queda conectado a su cuenta. Es una función legítima. Microsoft la tiene para equipos sin teclado, como salas de reuniones o impresoras.

**El abuso.** En Kali365, el «televisor» es el computador del delincuente. Él pide el código y se lo manda a usted por correo. Si usted lo escribe en la página **real** de Microsoft, conecta **el equipo del ladrón a su cuenta**.

**El engaño, en cuatro pasos:**

1. **El anzuelo.** Llega un correo que parece de SharePoint, OneDrive, Teams, DocuSign, Adobe Sign, un buzón de voz o una factura. Viene en 14 idiomas, incluido el español, y a veces está escrito con ayuda de inteligencia artificial. *(Hecho: Arctic Wolf y FBI.)*
2. **El código.** Una página le muestra un código y le pide escribirlo en la página de Microsoft «para ver el documento». *(Hecho.)*
3. **La página real.** Usted entra a la página verdadera de Microsoft, escribe el código, pone su clave y aprueba la verificación en dos pasos en su celular. Todo parece normal porque **es** normal. *(Hecho.)*
4. **Las llaves.** El delincuente recibe las llaves de su cuenta y entra a su correo, Teams y OneDrive. *(Hecho: FBI.)*

> **La regla de oro:** nunca escriba ni pegue un código que le llegó por correo, chat o una página web. Un código así solo es legítimo cuando **usted** está configurando un equipo que tiene delante (un televisor, una impresora, una sala de reuniones) y el código aparece en la pantalla de ese mismo equipo. *(Propuesta, basada en el mecanismo que describen el FBI y Microsoft.)*

## 3. Por qué la verificación en dos pasos no alcanza

Lo que recibe el delincuente funciona como **la manilla de un concierto**: mientras la tenga puesta, nadie le vuelve a pedir la boleta.

- **Hecho.** El FBI explica que el atacante entra «sin necesitar una contraseña ni completar más verificaciones» de dos pasos.
- **Hecho.** La verificación en dos pasos sigue siendo indispensable contra el robo de contraseñas. Pero aquí es la **propia persona** quien aprueba la entrada.
- **Consecuencia:** si alguien cae, **cambiar la contraseña no basta**. Hay que «anular la manilla»: TI debe cerrar todas las sesiones abiertas de la cuenta.

## 4. Qué hace el delincuente una vez adentro

- **Hecho (Arctic Wolf):**
  - Lee el correo y copia los contactos.
  - Envía engaños a esos contactos desde la cuenta real.
  - Busca conversaciones de pagos para desviar dinero: es el fraude del «falso proveedor» o «falso jefe» (en inglés, *BEC*).
- **Hecho:** crea reglas en el buzón que **esconden** los correos con palabras como «spam», «phish» o «link». Así la víctima no ve los avisos de seguridad.
- **Hecho:** registra un equipo nuevo en la cuenta para quedarse más tiempo.
- **Hecho (MITRE):** el kit trae un módulo de inteligencia artificial que califica los correos robados y redacta respuestas para el fraude.

## 5. Qué hacer: cuatro fases, con responsable y plazo

> «No preguntes solamente si ocurrió. Pregunta si resistiríamos si ocurriera.»

### Prevenir: antes de que llegue el correo

| Qué hacer | Quién | Cuándo | Cómo sabemos que se hizo | Origen |
|---|---|---|---|---|
| Revisar quién usa hoy el inicio de sesión con código | TI | Esta semana | Hay una lista de usos legítimos, cada uno con dueño | Fuente (FBI) |
| Desactivarlo para todos, con excepciones con nombre propio (por ejemplo, las salas de reuniones) | TI, con el visto bueno de gerencia | Esta semana | La regla está activa y la lista de excepciones es corta | Fuente (Arctic Wolf, FBI y Microsoft) |
| Activar las alertas de Microsoft por inicios de sesión riesgosos | TI | Esta semana | Las alertas están encendidas | Fuente (Arctic Wolf) |
| Llaves de seguridad o *passkeys* para gerencia, finanzas y administradores | TI y gerencia (presupuesto) | Este trimestre | Porcentaje de cuentas importantes con llave | Fuente (CISA) |
| Charla y simulacro con la regla de oro | Gerencia y TI | Este mes y cada año | Asistencia y evaluación | Fuente (Arctic Wolf y CISA) |
| Confirmar por teléfono **todo** cambio de cuenta bancaria | Gerencia y finanzas | Desde hoy | Procedimiento escrito y registro de llamadas | Propuesta |
| Hacer la lista de asistentes de IA y apps con acceso a las cuentas | Responsable de IA | Este mes | Lista con app, permisos y dueño | Propuesta |

### Detectar: darse cuenta en minutos

| Qué hacer | Quién | Cuándo | Cómo sabemos que se hizo | Origen |
|---|---|---|---|---|
| Reportar cualquier código o inicio de sesión que usted no pidió | Todo el personal | En el momento | TI recibe reportes, y rápido | Fuente (CISA) |
| Alerta por inicios de sesión con código fuera de las excepciones | TI o su proveedor | Siempre activa | Alguien con nombre recibe y revisa la alerta | Fuente (Arctic Wolf) |
| Alerta por reglas de buzón que esconden correos, y por equipos nuevos en una cuenta | TI o su proveedor | Siempre activa | Las alertas existen y se revisan | Fuente (Arctic Wolf) |
| Revisar el registro de uso del asistente de IA: preguntas raras o a deshoras | Responsable de IA | Cada semana | Informe semanal | Propuesta |
| Desconfiar de todo cambio de cuenta bancaria pedido por correo | Finanzas | Siempre | Registro de verificaciones | Propuesta |

### Contener: la primera hora, si alguien cayó

| Qué hacer | Quién | Cuándo | Cómo sabemos que se hizo | Origen |
|---|---|---|---|---|
| Avisar a TI de inmediato, sin borrar el correo y sin intentar arreglarlo solo | La persona afectada | Minuto 0 | Ticket abierto con el correo guardado | Fuente (CISA y FBI) |
| Cerrar **todas** las sesiones y suspender la cuenta un rato (un «pase» puede durar hasta 1 hora) | TI | Primeros 15 minutos | Hora exacta del cierre | Fuente (Microsoft) |
| Borrar las reglas de buzón y los reenvíos raros; quitar equipos y apps desconocidos | TI | Primera hora | Antes y después documentados | Fuente (Microsoft) |
| Frenar los pagos con cambio de cuenta; llamar al banco si ya salió dinero | Finanzas y gerencia | Primera hora | Registro de llamadas | Propuesta |
| Quitar los permisos que esa cuenta les dio a asistentes o agentes de IA | Responsable de IA | Primera hora | Lista de permisos quitados | Propuesta |

### Recuperar: los días siguientes

| Qué hacer | Quién | Cuándo | Cómo sabemos que se hizo | Origen |
|---|---|---|---|---|
| Rehacer la verificación en dos pasos (mejor con llave) y la contraseña; devolver el acceso | TI | Día 1 | La cuenta funciona con métodos nuevos | Fuente (Microsoft y CISA) |
| Revisar qué se leyó y qué salió de la cuenta, incluido lo que se le preguntó al asistente de IA | TI y responsable de IA | Días 1 a 3 | Informe corto del alcance | Fuente (CISA) + propuesta (IA) |
| Llamar a clientes y proveedores que pudieron recibir instrucciones de pago falsas | Gerencia y finanzas | Día 1 | Lista de contactos avisados | Propuesta |
| Denunciar y reportar (ver la sección 7.2) | Gerencia y área legal | Primeros días | Números de radicado | Propuesta (canales oficiales verificados) |
| Reunión sin culpables: qué falló y qué cambiamos | Gerencia, TI y personal | A las 2 semanas | Acta con cambios, dueño y fecha | Propuesta |

### Si usted es usuario: cuatro hábitos

1. No escriba códigos que no pidió.
2. Confirme por otro canal: llame a un número que ya conoce, no al que viene en el correo.
3. Si Microsoft le pregunta por una app o un equipo que no reconoce, elija **Cancelar**.
4. Reporte en minutos, no en días. Reportar un error no es confesar una culpa.

### Si usted es empresario: tres decisiones para esta semana

1. Autorizar a TI a desactivar el inicio de sesión con código, con excepciones aprobadas.
2. Firmar la regla de pagos: todo cambio de cuenta bancaria se confirma por teléfono.
3. Decir en voz alta que reportar rápido **no** tiene castigo.

## 6. La inteligencia artificial en esta historia

- **Hecho: la IA ayuda al delincuente.** Escribe correos sin errores, adaptados al cargo de cada víctima y en muchos idiomas. Ya no sirve buscar faltas de ortografía para detectar un engaño (Arctic Wolf, FBI, Microsoft).
- **Hecho: el asistente de IA ve lo que usted ve.** Los asistentes de IA de las empresas, como Microsoft 365 Copilot, muestran solo los datos que la persona tiene permiso de ver (Microsoft). Suena bien, pero tiene otra cara: **si roban su cuenta, también le roban a «su» asistente**.
- **Hipótesis:** con la cuenta robada, el delincuente podría preguntarle al asistente «resúmeme los pagos pendientes y las cuentas bancarias de los proveedores» y obtener en segundos lo que antes le tomaba horas. También podría dejar un documento con instrucciones escondidas para que el asistente las siga después, cuando otra persona le pregunte. No hay evidencia pública de que Kali365 lo haya hecho, pero se ha demostrado en ejercicios de investigación sobre Copilot.
- **Qué hacer:**
  - Tener una lista de qué asistentes y agentes de IA hay y qué pueden leer.
  - Revisar su registro de uso.
  - Incluirlos en el plan de respuesta: si cae una cuenta, también se revisa la IA.

## 7. Tres insights

### 7.1 El crimen como negocio: phishing por suscripción

- **Hecho (Arctic Wolf):** Kali365 funciona como una franquicia de tres niveles.
  - El **autor** crea la plataforma.
  - Los **revendedores** consiguen clientes.
  - Los **afiliados** pagan US$250 al mes (o US$2.000 al año), envían los correos y cobran el fraude.
- **Hecho:**
  - Se paga en criptomonedas sin verificar quién paga.
  - La cuenta del delincuente se recupera por Telegram.
  - Las cuentas robadas se pueden **pasar de un afiliado a otro**.
- **Hecho (SpyCloud):** cuando el FBI publicó su alerta, los operadores anunciaron su «retiro», pero siguieron con otro nombre.
- **Mi lectura:** ya no hace falta saber programar para atacar: basta con pagar. Por eso ninguna empresa es «demasiado pequeña» para ser blanco. Es el mismo patrón de «empresa criminal» que muestran la lista de buscados de la policía alemana (BKA) y la operación First Light de INTERPOL: cada eslabón hace una parte y todos cobran.

### 7.2 Dónde reportar

Primero adentro, después afuera. Todo verificado el 05/11/2026 en sitios oficiales.

| Si pasó esto… | Avise a… | Cómo |
|---|---|---|
| Recibió el correo o escribió el código | **TI de su empresa**, de inmediato | El canal interno, o el botón **«Reportar»** de Outlook si su empresa lo activó |
| Su empresa sufrió un incidente | **ColCERT**, el equipo nacional de respuesta del Ministerio TIC | contacto@colcert.gov.co. Los correos de phishing se reenvían a phishing-report@colcert.gov.co |
| Quiere orientación o reportar a la Policía | **CAI Virtual de la Policía Nacional** | https://caivirtual.policia.gov.co. El 05/11/2026 el portal estaba en mantenimiento y atendía por WhatsApp, al 323 273 3411 |
| Quiere poner la denuncia penal | **¡ADenunciar!** (Policía y Fiscalía, incluye delitos informáticos) o la **Fiscalía** | https://adenunciar.policia.gov.co/Adenunciar/, o Denuncia Fácil de la Fiscalía, o la línea 122 desde el celular |
| Se expusieron datos personales de clientes o empleados | **Superintendencia de Industria y Comercio (SIC)** | Reporte de incidentes de seguridad **dentro de los 15 días hábiles** siguientes a la detección |
| Salió dinero | **Su banco**, de inmediato | Por teléfono, con los datos de la transferencia *(propuesta)* |
| La página falsa o el bot del delincuente | **Cloudflare** y **Telegram** | abuse.cloudflare.com (categoría Phishing) y abuse@telegram.org |
| Referencia internacional | **FBI IC3**, que también recibe denuncias de personas fuera de EE. UU. | https://www.ic3.gov |

Fuentes: colcert.gov.co, policia.gov.co, fiscalia.gov.co, sic.gov.co, learn.microsoft.com, developers.cloudflare.com, telegram.org e ic3.gov.

### 7.3 Qué dice la ley en Colombia

> Esto **no es asesoría legal**. Cito las normas oficiales. Si se aplican a un caso concreto, lo deciden la Fiscalía y un juez.

**Para los delincuentes** (Ley 1273 de 2009, de delitos informáticos). La ley castiga, entre otras conductas:

| Conducta del kit | Artículo | Pena según el texto oficial |
|---|---|---|
| Diseñar, desarrollar, vender o enviar páginas o enlaces falsos para capturar datos | 269G, suplantación de sitios web | 48 a 96 meses de prisión |
| Producir, vender o comprar «software malicioso» | 269E, uso de software malicioso | 48 a 96 meses |
| Entrar sin permiso a la cuenta | 269A, acceso abusivo | 48 a 96 meses |
| Obtener, vender o intercambiar datos personales | 269F, violación de datos personales | 48 a 96 meses |
| Robar suplantando al usuario ante los sistemas de acceso | 269I, hurto por medios informáticos | Las penas del hurto calificado |
| Desviar un pago, o fabricar o facilitar el programa para hacerlo | 269J, transferencia no consentida de activos | 48 a 120 meses |

- **Mi lectura (hipótesis):** los tres niveles del negocio podrían responder penalmente.
  - El **autor del kit**, por diseñarlo y venderlo.
  - Los **revendedores**, por traficarlo.
  - Los **afiliados**, por usarlo.
  - El Código Penal también castiga a los partícipes (art. 30) y el «concierto para delinquir» (art. 340).
- **Hecho:** Colombia aprobó el Convenio de Budapest sobre ciberdelincuencia (Ley 1928 de 2018). Rige desde 2020 y sirve para pedir ayuda a otros países, porque estos delincuentes casi nunca están en Colombia.

**Para la empresa víctima.** La empresa no es delincuente: es víctima. Pero tiene deberes.

- **Hecho, Ley 1581 de 2012 (habeas data), art. 17:** quien maneja datos personales de clientes o empleados debe:
  - Guardarlos con «condiciones de seguridad» que impidan el acceso no autorizado.
  - Informar a la autoridad cuando haya fallas de seguridad con riesgo para esos datos.
- **Hecho:** según la SIC, ese reporte se hace dentro de los **15 días hábiles** siguientes a la detección.
- **Hecho, art. 23:** la SIC puede imponer multas de hasta 2.000 salarios mínimos, suspender actividades o cerrar.
- **Mi lectura:** las medidas de la sección 5, con su evidencia (la regla activa, las alertas y la asistencia a la charla), también muestran que la empresa **fue diligente**.
- **Por verificar con un abogado:** si la empresa responde ante clientes o proveedores que pierdan dinero por un correo falso enviado desde su cuenta. Depende del contrato y del caso.

**Fuentes legales (texto oficial, consultado el 05/11/2026):**
- Ley 1273 de 2009. http://www.secretariasenado.gov.co/senado/basedoc/ley_1273_2009.html
- Ley 1581 de 2012. http://www.secretariasenado.gov.co/senado/basedoc/ley_1581_2012.html
- Ley 1928 de 2018. http://www.secretariasenado.gov.co/senado/basedoc/ley_1928_2018.html
- Código Penal (Ley 599 de 2000). http://www.secretariasenado.gov.co/senado/basedoc/ley_0599_2000.html
- Consejo de Europa, tratado 185 (Convenio de Budapest), estado de firmas y ratificaciones. https://www.coe.int/en/web/conventions/full-list?module=signatures-by-treaty&treatynum=185
- SIC, Circular Única, Título V. https://sedeelectronica.sic.gov.co/transparencia/normativa/titulo-v-actualizado-el-9-de-enero-de-2026

## 8. Glosario corto

| Palabra | En simple |
|---|---|
| **Sesión** | El «pase» que mantiene la cuenta abierta sin pedir la clave otra vez. |
| **Token** | La «manilla del evento» digital: prueba de que ya entró. Quien la tiene, entra. |
| **Código de dispositivo** | El código que se escribe para conectar un televisor o una sala de reuniones a una cuenta. |
| **Verificación en dos pasos (MFA)** | La clave más una confirmación en el celular. |
| **Llave de seguridad o *passkey*** | Una verificación que solo funciona en el sitio legítimo. Frena las páginas falsas intermediarias. |
| **Phishing** | Un engaño por correo, chat o página web para robar datos o accesos. |
| **PhaaS** | «Phishing como servicio»: un kit de engaño que se alquila. |
| **BEC** | El fraude del «falso jefe» o «falso proveedor» que pide pagar a otra cuenta. |
| **Asistente o agente de IA** | Un programa de IA que lee y, a veces, actúa en nombre de una persona, con sus permisos. |

## 9. Fuentes

- Arctic Wolf Labs, «Token Bingo», 24/04/2026. https://arcticwolf.com/resources/blog/token-bingo-dont-let-your-code-be-the-winner/
- CISA, NSA, FBI y MS-ISAC, guía de phishing «Stopping the Attack Cycle at Phase One», octubre de 2023. https://www.cisa.gov/sites/default/files/2025-03/Phishing%20Guidance%20-%20Stopping%20the%20Attack%20Cycle%20at%20Phase%20One%20508.pdf
- FBI IC3, alerta I-052126-PSA, 21/05/2026. https://www.ic3.gov/PSA/2026/PSA260521
- MITRE ATT&CK, «Kali365» (S9044). https://attack.mitre.org/software/S9044/
- Microsoft Learn, inicio de sesión con código de dispositivo y acceso condicional. https://learn.microsoft.com/en-us/entra/identity/conditional-access/concept-authentication-flows
- Microsoft Learn, privacidad y permisos de Microsoft 365 Copilot. https://learn.microsoft.com/en-us/copilot/microsoft-365/microsoft-365-copilot-privacy
- SpyCloud, análisis de Kali365, 11/06/2026. https://spycloud.com/blog/kali365-anatomy-of-a-microsoft365-phishing-as-a-service-kit/
- Las fuentes de la sección 7 (dónde reportar y marco legal) están junto a cada dato.

---
*Este documento es educativo. No es asesoría legal ni reemplaza a su equipo de TI o a su abogado.*
