# Línea de tiempo: Kali365 (2026)

> **Módulo 2** · Jhon Fredy León García · 05/11/2026
> Regla del curso, *«reconstruir sin inventar»*: cada fecha lleva su fuente. Si un dato viene de un solo proveedor, lo indico.
> Relacionado: [incidente analizado](incidente-analizado.md) · [mapa del ecosistema](mapa-ecosistema.png).

| Fecha | Qué pasó | Fuente | Tipo |
|---|---|---|---|
| **15/02/2026** | Primera actividad con la marca Kali365 | SpyCloud (11/06/2026) | Hecho de un solo proveedor |
| **Finales de marzo de 2026** | Entre 300 y 500 cuentas capturadas al día | SpyCloud | Hecho de un solo proveedor |
| **25/03/2026** | Microsoft Learn actualiza su guía y recomienda **bloquear el flujo de código de dispositivo siempre que sea posible** | Microsoft Learn | Hecho (contexto) |
| **Abril de 2026** | El FBI y MITRE ven Kali365 por primera vez. Arctic Wolf empieza a seguir una campaña grande de phishing con código de dispositivo que sale casi toda de una sola IP | FBI IC3, MITRE S9044, Arctic Wolf | Hecho |
| **06/04/2026** | Microsoft documenta otra campaña de phishing con código de dispositivo **habilitada con IA**, la del kit EvilTokens | Microsoft Security Blog | Hecho (técnica parecida, otro kit) |
| **24/04/2026** | Arctic Wolf publica «Token Bingo»: el panel Kali365 Live, el modelo de tres niveles, el precio de USD 250 por 30 días, los modos código y cookie, y los IOC | Arctic Wolf Labs | Hecho |
| **06 al 27/05/2026** | Están activos 126 servidores del mismo operador | Arctic Wolf (02/06/2026) | Hecho |
| **18 al 27/05/2026** | Huntress ve inicios con código de dispositivo desde Tencent Cloud. El pico es el **20/05**, con más de 80 accesos exitosos entre sus clientes | Huntress (11/06/2026) | Hecho |
| **21/05/2026** | El **FBI publica la alerta I-052126-PSA** y recomienda bloquear el flujo de código de dispositivo | FBI IC3 | Hecho |
| **21/05/2026** | Unos 13 segundos después de reenviar la alerta del FBI, los operadores anuncian su «retiro». Ya habían migrado a la marca «Green Octopus» | SpyCloud | Hecho de un solo proveedor |
| **02/06/2026** | Arctic Wolf documenta la expansión a páginas falsas de Okta, Xerox DocuShare y otros servicios | Arctic Wolf Labs | Hecho |
| **11/06/2026** | Huntress y SpyCloud publican análisis completos del kit. Huntress mide **42 segundos** entre el clic y el acceso | Huntress, SpyCloud | Hecho |
| **30/07/2026** | MITRE ATT&CK cataloga Kali365 como software **S9044**, con 17 técnicas y subtécnicas | MITRE ATT&CK | Hecho |
| **02/09/2026** | Barracuda reporta «muchos ataques de Kali365 en los últimos meses» | Barracuda | Hecho |
| **04/11/2026** | El profesor comparte en el curso el video de SOCFortress sobre Kali365 | Grupo del curso | Hecho |
| **05/11/2026** | La clase 5 (Módulo 2, Sesión 2) usa Kali365 como laboratorio de defensa proactiva | Presentación de la clase | Hecho |

## Lectura de la línea de tiempo

- **[HECHO]** La guía de Microsoft para bloquear el flujo es del 25/03/2026, **antes** de la alerta del FBI. El control existía desde antes del pico de ataques.
- **[HECHO]** El «retiro» de la marca no detuvo la técnica: SpyCloud vio que siguió la actividad y Barracuda reportó ataques hasta septiembre.
- **[SUPOSICIÓN]** Entre la primera señal pública (abril) y la alerta oficial (mayo) pasaron semanas. Una empresa con defensa proactiva habría aplicado el bloqueo con las primeras publicaciones, sin esperar la alerta del FBI.

> Las fuentes completas están en [incidente-analizado.md](incidente-analizado.md#fuentes).
