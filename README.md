[← Volver al laboratorio](../README.md)

# Malicious PDF — Ingeniería social con documentos trampa

![SET](https://img.shields.io/badge/Social--Engineer%20Toolkit-Concepto-c084fc?style=flat-square)
![Phishing](https://img.shields.io/badge/Ataque-Phishing%20dirigido-f472b6?style=flat-square)
![Defensa](https://img.shields.io/badge/Enfoque-An%C3%A1lisis%20y%20Defensa-60a5fa?style=flat-square)
![Estado](https://img.shields.io/badge/Entorno-Laboratorio%20Aislado-34d399?style=flat-square)

> [!WARNING]
> Este repositorio **no contiene** el archivo PDF ni los comandos de explotación. Documenta la metodología y el análisis defensivo de un ataque de documento malicioso, realizado en un entorno de laboratorio aislado y con fines educativos. No se incluye ningún artefacto funcional.

## Objetivo

Entender cómo funciona un ataque de ingeniería social basado en documentos (PDF trampa) generado con el **Social-Engineer Toolkit (SET)**, identificar sus indicadores de compromiso (IOCs) y documentar qué controles defensivos lo detectan o lo bloquean.

## ¿Qué es un PDF malicioso?

Un PDF malicioso es un documento que parece legítimo pero que contiene código embebido (JavaScript, un exploit de la aplicación lectora, o un payload enlazado) diseñado para ejecutarse cuando la víctima lo abre. Suele distribuirse mediante **phishing**: un correo o mensaje que convence a la víctima de abrir el archivo.

**SET (Social-Engineer Toolkit)** es una herramienta de código abierto, orientada a pruebas de ingeniería social autorizadas (*red team* / *pentesting*), que automatiza la creación de este tipo de vectores de ataque.

## Entorno de laboratorio

| Elemento | Detalle |
|---|---|
| Entorno | Máquinas virtuales aisladas, sin acceso a redes de producción |
| Atacante | Kali Linux con SET |
| Víctima | Máquina virtual de prueba, con snapshot previo |
| Autorización | Laboratorio académico, sin víctimas reales |

> [!IMPORTANT]
> Un ataque de este tipo realizado fuera de un entorno autorizado es ilegal. Esta práctica se limitó a un entorno cerrado y propio.

## Metodología (a nivel conceptual)

1. **Reconocimiento:** identificar el pretexto de ingeniería social (qué documento resultaría creíble para la víctima objetivo).
2. **Generación del vector:** uso de SET para asociar un payload a un documento PDF.
3. **Entrega:** simulación del método de distribución (por ejemplo, correo con el archivo adjunto).
4. **Ejecución controlada:** apertura del documento en la máquina víctima, dentro del laboratorio.
5. **Observación:** análisis del comportamiento resultante (conexión saliente, proceso creado, persistencia).

## Análisis e indicadores de compromiso (IOCs)

<!-- Completa esta sección con lo que observaste en tu laboratorio -->

| Indicador | Observación |
|---|---|
| Proceso anómalo | *(ej. el lector de PDF genera un proceso hijo inesperado)* |
| Conexión de red | *(ej. conexión saliente hacia la IP del atacante en el puerto de escucha)* |
| Artefactos en disco | *(ej. archivos temporales creados fuera de lo habitual)* |
| Alertas del antivirus/EDR | *(ej. detección por firma o por comportamiento)* |

## Controles defensivos

- **Capacitación del usuario:** reconocer correos y adjuntos inesperados, incluso de remitentes aparentemente conocidos.
- **Antivirus / EDR actualizado:** detección por firma y por comportamiento.
- **Sandboxing de adjuntos:** abrir documentos sospechosos en un entorno aislado antes de distribuirlos al usuario final.
- **Deshabilitar JavaScript en el lector de PDF**, cuando el flujo de trabajo lo permita.
- **Filtrado de correo (gateway):** bloqueo de adjuntos con patrones conocidos de explotación.
- **Segmentación de red:** limitar el movimiento lateral si un equipo se compromete.

## Lo aprendido

Este laboratorio permitió entender el ciclo completo de un ataque de ingeniería social basado en documentos: desde la construcción del pretexto hasta la ejecución y los indicadores que deja. Reforzó que la defensa más efectiva combina capas técnicas (EDR, sandboxing, filtrado de correo) con la concientización del usuario, ya que este tipo de ataque explota la confianza antes que una vulnerabilidad técnica pura.

---

## 👨‍💻 Autor

**Neury Jorge Montero Mejía**

*Estudiante de Tecnólogo en Seguridad Informática*

[![LinkedIn](https://img.shields.io/badge/LINKEDIN-NEURY%20MONTERO-0A66C2?style=flat-square&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/neuryjmm)
[![GitHub](https://img.shields.io/badge/GITHUB-NEURYJMM-111111?style=flat-square&logo=github&logoColor=white)](https://github.com/NeuryJMM)

---


[← Volver al laboratorio](../README.md)
