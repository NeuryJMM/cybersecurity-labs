[← Volver al laboratorio](../README.md)

<div align="center">

# Cybersecurity Labs

![WebApp Security](https://img.shields.io/badge/WEBAPP-SECURITY-c084fc?style=flat-square)
![Ethical Hacking](https://img.shields.io/badge/ETHICAL-HACKING-f472b6?style=flat-square)
![Tampering](https://img.shields.io/badge/TAMPERING-60a5fa?style=flat-square)
![Docker](https://img.shields.io/badge/DOCKER-COMPOSE%20%2F%20DOCKERFILE-8b5cf6?style=flat-square&logo=docker&logoColor=white)
![Estado](https://img.shields.io/badge/ESTADO-EN%20PROGRESO-fbbf24?style=flat-square)

</div>

---

## Descripción general

**Cybersecurity Labs** reúne laboratorios prácticos de seguridad ofensiva y defensiva, realizados en entornos controlados y aislados (máquinas virtuales y contenedores Docker), con fines educativos.

Los laboratorios cubren ataques comunes contra aplicaciones web (XSS, SQL Injection), seguridad en Linux, ingeniería social con documentos trampa y análisis defensivo de los hallazgos obtenidos en cada práctica.

> [!WARNING]
> Todo el contenido de este repositorio se ejecutó en **entornos de laboratorio aislados**, sin acceso a sistemas de producción ni a terceros. Ninguna práctica incluye artefactos maliciosos funcionales, credenciales reales ni objetivos fuera del entorno propio. El enfoque es **aprender a identificar y mitigar** estos vectores, no replicarlos fuera de un entorno autorizado.

---

## Laboratorios

| # | Práctica | Tecnologías y alcance |
|:--:|---|---|
| 01 | [Dictionary Attacks](./dictionary-attacks) | Ataques de fuerza bruta por diccionario contra un servicio de autenticación, en un entorno controlado. |
| 02 | [Cross Site Scripting (XSS)](./cross-site-scripting) | Identificación y explotación controlada de XSS en una aplicación web vulnerable de laboratorio. |
| 03 | [SQL Injection](./sql-injection) | Identificación y explotación controlada de inyección SQL en una aplicación web vulnerable de laboratorio. |
| 04 | [Defensive Analysis](./defensive-analysis) | Análisis defensivo: detección, indicadores de compromiso y mitigación de los hallazgos de las prácticas anteriores. |
| 06 | [Malicious PDF](./06-pdf-malicioso-SET) | Metodología y análisis defensivo de un ataque de ingeniería social basado en documentos (sin artefactos maliciosos). |

---

## Entorno de laboratorio

| Elemento | Detalle |
|---|---|
| Aislamiento | Máquinas virtuales y contenedores Docker, sin acceso a redes de producción |
| Aplicaciones vulnerables | Desplegadas con Docker Compose / Dockerfile para pruebas repetibles |
| Autorización | Entornos propios o aplicaciones deliberadamente vulnerables para práctica |

---

## Estructura del repositorio

```text
cybersecurity-labs/
├── dictionary-attacks/
├── cross-site-scripting/
├── sql-injection/
├── defensive-analysis/
├── malicious-pdf/
└── README.md
```

Cada carpeta contiene su propio `README.md` con el objetivo, el entorno, la metodología, los hallazgos y las conclusiones de la práctica.

---

## Conclusión

Cybersecurity Labs documenta el trabajo práctico en seguridad ofensiva y defensiva: desde ataques comunes contra aplicaciones web y sistemas Linux, hasta vectores de ingeniería social y su correspondiente análisis defensivo.

Cada práctica se realizó en un entorno aislado y se documenta con su metodología, los hallazgos obtenidos y los controles que permiten detectarlos o mitigarlos. El resultado es un repositorio de referencia que demuestra experiencia práctica tanto en identificar vulnerabilidades como en defenderse de ellas.

---


## 👨‍💻 Autor

**Neury Jorge Montero Mejía**

*Estudiante de Tecnólogo en Seguridad Informática*

[![LinkedIn](https://img.shields.io/badge/LINKEDIN-NEURY%20MONTERO-0A66C2?style=flat-square&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/neuryjmm)
[![GitHub](https://img.shields.io/badge/GITHUB-NEURYJMM-111111?style=flat-square&logo=github&logoColor=white)](https://github.com/NeuryJMM)

---


[← Volver al laboratorio](../README.md)
