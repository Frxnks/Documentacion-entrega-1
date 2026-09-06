# Registro de prompts - Proyecto MIRA

Este archivo registra los prompts utilizados para analizar, decidir y documentar los requisitos del proyecto. Se mantiene separado del README para conservar trazabilidad del trabajo asistido por IA.

## Prompt 001 - Analisis inicial del documento

**Fecha:** 06-09-2026  
**Tecnica relacionada:** Grounded Prompting  
**Estado:** Registrado

### Prompt

> Mediante el documento que te pasare, donde se realiza un analisis de la situacion y se identificaron 10 inconsistencias y las respuestas del cliente, es decir, yo Nikolas, analizalo y luego te dare instrucciones.

### Proposito

Establecer el contexto del caso MIRA y pedir un analisis basado exclusivamente en el documento recibido, sin producir todavia entregables ni modificar archivos.

## Prompt 002 - Preparacion del README del Paso 1

**Fecha:** 06-09-2026  
**Tecnica relacionada:** Grounded Prompting y Structured Output  
**Estado:** Registrado

### Prompt

> Ya con eso podemos empezar con el Paso 1. Adaptar el material recibido a un README.md bien hecho y crear otra cosa donde se registren todos los prompts que te estoy dando, como este.

### Proposito

Convertir el analisis entregado en una documentacion ordenada del Paso 1 y crear un registro persistente de prompts para las siguientes etapas.

### Resultado

- Se actualizo `README.md` con el alcance, los criterios acordados, las 10 inconsistencias, las respuestas de negocio y la bitacora DEC-01 a DEC-10.
- Se creo este archivo `PROMPTS.md` para continuar registrando los prompts reales.
- Se dejo visible una observacion sobre la trazabilidad H-02/DEC-02 para validarla antes del cierre.

## Plantilla para nuevos prompts

### Prompt NNN - Titulo

**Fecha:** DD-MM-AAAA  
**Tecnica relacionada:** Tecnica de prompting  
**Estado:** Registrado / Ejecutado / Validado

#### Prompt

> Texto exacto del prompt utilizado.

#### Proposito

Que se buscaba obtener y sobre que fuentes se debia fundamentar.

#### Resultado y validacion

Salida obtenida, artefacto afectado y comprobacion realizada.