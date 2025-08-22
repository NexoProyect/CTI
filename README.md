# 🔎 Cyber Threat Intelligence (CTI) Blog Series

Bienvenido a mi repositorio de CTI Blogs, una serie de investigaciones prácticas sobre amenazas digitales reales.
El propósito de este proyecto es documentar, analizar y compartir conocimientos en torno al mundo del Cyber Threat Intelligence (CTI), mostrando casos reales de estafas, campañas maliciosas y técnicas utilizadas por ciberdelincuentes.

# 📂 Estructura del Repositorio

🧭: Esquema canónico de CTI ID

Convención:

```bash
[MODALIDAD]-[SECCIÓN]-[IDENTIFICADOR]
```

`MODALIDAD` → Enfoque del análisis (p. ej., `01`=Web, `02`=Malware, `03`=App, `04`=Infra, `05`=Phishing/Ingeniería social, etc.).


`SECCIÓN` → Periodo del año (letra).


`IDENTIFICADOR` → Correlativo de tres dígitos dentro de la combinación MODALIDAD + SECCIÓN (`001`, `002`, `003`…).


Ejemplos:

`CTI ID: 01-A-001` → Web · Q1 (Ene–Mar) · Caso 001 — (xmailline-verfo0l.zya.me)

`CTI ID: 02-B-002` → Malware · Q2 (Abr–Jun) · Caso 002

`CTI ID: 04-C-003` → Infra · Q3 (Jul–Sep) · Caso 003

| Código | Modalidad (enfoque) | Descripción breve                      |
| -----: | ------------------- | -------------------------------------- |
|     01 | Web                 | Sitios, phishing web, hosting, HTML/JS |
|     02 | Malware             | Binarios, loaders, RAT, ransomware     |
|     03 | App                 | Móvil/desktop, empaquetado, permisos   |
|     04 | Infra               | C2, dominios, IP ranges, TTPs infra    |
|     05 | Phishing/Social     | Campañas, plantillas, señuelos         |


Periodos del año operativos :
| Sección | Rango de fechas   |
| :-----: | ----------------- |
|    A    | Período 1         |
|    B    | Período 2         |
|    C    | Período 3         |
|    D    | Período 4         |


Dentro de cada carpeta encontrarás:

- 📖 Informe técnico detallado (Markdown)

- 🖼️ Evidencias y capturas del caso

- 🔑 Resumen práctico para usuarios no técnicos

🎯 Objetivos

Mostrar cómo se detectan y analizan amenazas en la vida real.

Educar tanto a técnicos como a personas sin conocimientos avanzados.

Contribuir a la comunidad con ejemplos prácticos de investigación en CTI.

# 📖 Casos publicados

- CTI ID: 01-A-001 – Phishing en xmailline-verfo0l.zya.me
➝ Análisis de una web de phishing que imitaba a Outlook para robar credenciales y datos bancarios.
📄 [Leer informe](https://github.com/NexoProyect/CTI/blob/main/01/a-001/cti.md)

(La lista crecerá a medida que se publiquen nuevos análisis)

# 🛡️ Para quién es este repositorio:

🔐 Profesionales de ciberseguridad que buscan ejemplos reales de investigación.

📚 Estudiantes que quieren aprender sobre CTI de forma práctica.

👩‍💻 Usuarios comunes interesados en consejos para protegerse.

🤝 Contribuciones

# Si deseas contribuir con nuevos casos, correcciones o mejoras:

- Haz un fork del repositorio.

- Crea una rama con tu caso o mejora.

- Envía un Pull Request con tu aporte.

# ⚠️ Disclaimer

Este repositorio tiene fines educativos y de concienciación.
No fomenta, ni apoya, ni promueve el uso malicioso de la información aquí compartida.

La inteligencia de amenazas no se queda en los laboratorios. Se comparte para protegernos todos.
