# Entrega 1 - Analisis de requisitos MIRA

**Proyecto:** MIRA, Plataforma multimodal de decisiones
**Empresa:** DPRIME SpA
**Equipo:** Dante Chavez, Nikolas Lagos, Franko Moraga y Felipe Caru
**Fecha de analisis:** 06-09-2026
**Representante del cliente:** Nikolas Lagos

## 1. Proposito y alcance

Esta entrega analiza el documento de necesidades de MIRA y sus entrevistas con los participantes del negocio. El objetivo es identificar contradicciones, resolverlas desde la perspectiva del cliente y dejar una base trazable para la especificacion tecnica posterior.

El alcance considera la ingesta de expedientes y evidencias digitales, la evaluacion automatizada, la revision humana asistida, la configuracion de flujos y umbrales, la integracion con sistemas heredados, la seguridad de los datos y el plan de entregas.

La decision de negocio que orienta este documento es iniciar con un MVP de reembolsos medicos basado en documentos e imagenes en enero de 2027, y completar las capacidades multimodales, incluyendo audio y video, en abril de 2027.

## 2. Hallazgos ejecutivos

1. El cobro comercial se aplica solo a decisiones automaticas concluyentes; los casos enviados a revision humana no se cobran como validaciones resueltas.
2. La operacion real del cliente exige retencion de evidencia, residencia de datos, anonimización y control humano que no estaban suficientemente reflejados en la propuesta inicial.
3. El sistema debe responder asincronicamente, integrarse con una carpeta compartida del sistema heredado y ofrecer autenticacion federada, en lugar de depender exclusivamente de APIs, procesamiento sincrono y credenciales propias.

## 3. Criterios generales acordados

- Una validacion facturable es una decision automatica util, clara y concluyente.
- Los casos desfavorables o con señales de fraude no se rechazan automaticamente: pasan a revision humana.
- La evidencia permanece activa 90 dias y luego se conserva cifrada en almacenamiento frio durante 5 años.
- Los datos originales permanecen en Chile. Antes de consultar modelos externos se enmascaran los datos personales identificables.
- La ingesta entrega un acuse HTTP 202 en menos de 1 segundo y el resultado se notifica posteriormente.
- Los umbrales son configurables por flujo y cliente; para el piloto el umbral de aprobacion automatica es 90 puntos.
- La integracion debe soportar API y un adaptador para carpeta compartida.
- La autenticacion preferente para clientes corporativos es SSO federado.

## 4. Inconsistencias detectadas y respuesta del cliente

En todos los casos, la respuesta fue tomada por Nikolas Lagos en el rol de Representante del Cliente.

### H-01 - Definicion de validacion facturable

**Respuesta del cliente:** Se cobra solo cuando la maquina emite por si sola una decision util y concluyente. Un caso enviado a un analista humano no se cobra como validacion resuelta, porque el cliente debe realizar trabajo manual.

**Contradiccion:** La propuesta habla de resultados utiles y concluyentes, mientras que una entrevista plantea cobrar tambien los casos derivados a revision. Finanzas entiende que esos casos no deben cobrarse.

**Resolucion:** Se factura exclusivamente la decision automatica concluyente. Los casos derivados se registran para metricas operativas, sin cobro unitario.

**Impacto:** Facturacion, definicion de validacion, requisitos comerciales y backlog.

### H-02 - Tramos de volumen tarifario

**Respuesta del cliente:** Los tramos hasta 5.000 casos no representan la operacion real. El area medica procesa aproximadamente 12.000 casos y mas de 40.000 documentos al mes.

**Contradiccion:** La tabla inicial termina en 5.000 casos y no define un precio para operaciones de 10.000, 15.000 o mas casos.

**Resolucion:** La tabla tarifaria debe parametrizarse para soportar volumenes superiores a 15.000 casos mensuales, con precios definidos por volumen.

**Impacto:** Modelo comercial, configuracion de tarifas y escalabilidad operativa.

### H-03 - Retencion de evidencia

**Respuesta del cliente:** La evidencia debe estar disponible rapidamente durante los primeros tres meses y luego pasar a un archivo historico seguro durante 5 años.

**Contradiccion:** La propuesta indica eliminar documentos a los 90 dias, pero Cumplimiento exige conservarlos por al menos 5 años y, para algunos productos, hasta 10.

**Resolucion:** Se mantienen 90 dias en almacenamiento activo y luego se migra la evidencia cifrada a almacenamiento frio hasta completar 5 años.

**Impacto:** Retencion, almacenamiento por capas, auditoria y prueba de recuperacion.

### H-04 - Rechazo automatico

**Respuesta del cliente:** Ningun reembolso puede rechazarse de forma completamente automatica. Un puntaje bajo o una alerta de fraude debe llevar el caso a la pantalla del liquidador para que revise la evidencia y firme la decision.

**Contradiccion:** El documento permite rechazar automaticamente casos con puntaje bajo, mientras que Cumplimiento prohibe esa practica.

**Resolucion:** MIRA no emite rechazos automaticos. Todo caso desfavorable o sospechoso pasa a revision asistida con prioridad alta.

**Impacto:** Motor de decisiones, bandeja de revision, auditoria y derechos del asegurado.

### H-05 - Borrado de PII e inalterabilidad

**Respuesta del cliente:** Al ejercer el derecho de borrado se eliminan el RUT y el nombre de la pantalla, pero se conserva el historial tecnico de evaluacion sin identificar a la persona.

**Contradiccion:** La exigencia de mantener una auditoria inalterable parece entrar en conflicto con la eliminacion de datos personales.

**Resolucion:** Se anonimiza la identidad del titular y se conserva la traza tecnica inalterable.

**Impacto:** Privacidad, auditoria, anonimización y pruebas de borrado.

### H-06 - Residencia territorial de datos

**Respuesta del cliente:** Los datos originales de asegurados chilenos deben permanecer en Chile. Si se usan modelos externos, solo pueden enviarse textos o fragmentos sin nombres ni RUT.

**Contradiccion:** La arquitectura considera modelos alojados fuera del pais sin describir un control suficiente para impedir la transferencia de PII.

**Resolucion:** La evidencia se almacena localmente y un modulo local enmascara la PII antes de cualquier envio a modelos externos.

**Impacto:** Arquitectura de seguridad, privacidad, residencia de datos y pruebas de inspeccion de cargas.

### H-07 - Tiempo de procesamiento

**Respuesta del cliente:** No es realista procesar cualquier expediente en 5 segundos. Se necesita un comprobante inmediato y una notificacion cuando el procesamiento termine.

**Contradiccion:** La exigencia de respuesta sincrona en menos de 5 segundos no es compatible con expedientes pesados, imagenes numerosas o videos que tardan entre 30 segundos y varios minutos.

**Resolucion:** La API responde HTTP 202 en menos de 1 segundo y el procesamiento continua de forma asincrona mediante eventos, webhooks o mecanismo equivalente.

**Impacto:** API de ingesta, colas, notificaciones, idempotencia y experiencia de integracion.

### H-08 - Integracion con sistema heredado

**Respuesta del cliente:** El sistema central tiene 22 años y solo intercambia archivos mediante una carpeta compartida revisada cada media hora. No se debe exigir una modernizacion del sistema central.

**Contradiccion:** La propuesta asume integracion exclusivamente mediante API REST.

**Resolucion:** Se desarrolla un adaptador desacoplado que monitorea la carpeta, transforma los archivos y los entrega a MIRA.

**Impacto:** Ingesta, conector de carpeta, procesamiento de archivos y caso de uso CU-01.

### H-09 - Autenticacion duplicada

**Respuesta del cliente:** Los usuarios deben ingresar con las mismas credenciales corporativas. No se aceptara administrar una clave adicional dentro de MIRA.

**Contradiccion:** El documento propone credenciales administradas por MIRA, pero el cliente exige federacion con su directorio corporativo.

**Resolucion:** Se implementa autenticacion federada mediante SSO, SAML u OAuth2, segun el mecanismo acordado con el cliente.

**Impacto:** Seguridad, gestion de usuarios, roles y acceso corporativo.

### H-10 - Fechas de entrega

**Respuesta del cliente:** Se necesita operar con casos reales en enero y mostrar resultados en marzo, pero sin arriesgar la calidad. El primer lanzamiento debe limitarse a reembolsos medicos con documentos e imagenes; audio y video quedan para abril.

**Contradiccion:** El plan inicial ubicaba la marcha blanca en febrero y la produccion completa en abril, mientras que el compromiso comercial exige una capacidad util en enero.

**Resolucion:** Se adelanta un MVP de reembolsos medicos para enero de 2027 y se mantiene la entrega multimodal completa para abril de 2027.

**Impacto:** Alcance, backlog, planificacion de entregas y priorizacion MoSCoW.

## 5. Bitacora de decisiones

Las siguientes decisiones formalizan la resolucion de las inconsistencias desde el rol de Representante del Cliente.

| ID | Decision | Justificacion | Impacto principal |
| --- | --- | --- | --- |
| DEC-01 | Cobrar solo decisiones automaticas concluyentes. | Respeta la propuesta comercial y evita disputas de facturacion. | Facturacion, validacion, backlog |
| DEC-02 | Conservar evidencia 90 dias activa y 5 años en almacenamiento frio cifrado. | Cumple la exigencia regulatoria sin mantener todo en almacenamiento activo. | RQNF-004, retencion |
| DEC-03 | Prohibir rechazos automaticos y derivar casos desfavorables a revision humana. | Protege los derechos del asegurado y cumple la restriccion legal. | RQF-008, CU-02 |
| DEC-04 | Usar ingesta asincrona con acuse HTTP 202 y notificacion posterior. | Evita bloqueos y reintentos en expedientes pesados. | RQNF-001, SSD |
| DEC-05 | Anonimizar PII y conservar la traza tecnica de auditoria. | Reconcilia privacidad e inalterabilidad. | RQF-019, auditoria |
| DEC-06 | Mantener evidencia en Chile y enmascarar PII antes de consultar modelos externos. | Cumple la residencia territorial de los datos. | RQNF-006, arquitectura |
| DEC-07 | Hacer configurables los umbrales por flujo y usar 90 puntos en el piloto. | Permite ajustar el apetito de riesgo del cliente. | RQF-006, flujos |
| DEC-08 | Incorporar adaptador para carpeta compartida. | Permite integrar el sistema heredado sin reemplazarlo. | RQF-012, CU-01 |
| DEC-09 | Priorizar autenticacion federada SSO. | Evita credenciales duplicadas y cumple ciberseguridad corporativa. | RQF-020, seguridad |
| DEC-10 | Lanzar MVP medico en enero y completar audio/video en abril. | Cumple el hito comercial sin forzar una entrega inestable. | Backlog, roadmap |

### Observacion de trazabilidad

En el material fuente, `H-02` aparece rotulado en algunos lugares con `DEC-02`, pero el contenido de `DEC-02` corresponde a `H-03` (retencion de evidencia). Asimismo, `H-07` se relaciona con `DEC-04` por el procesamiento asincrono, mientras `DEC-07` corresponde a la configuracion de umbrales. Esta observacion debe validarse antes de cerrar la version final del informe y sus referencias cruzadas.

## 6. Resultado esperado para los siguientes pasos

Este README deja cerrada la base documental del Paso 1. El Paso 2 debera convertir estas decisiones en los artefactos tecnicos: modelo de dominio, casos de uso, requisitos, pruebas, backlog con DoD y diagrama SSD. El Paso 3 debera documentar las tecnicas de prompting y anexar los prompts utilizados.
