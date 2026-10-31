# Agente IA: proyecto integrador

Asistente de Calificación de Leads de **Control Data Chile**, construido en n8n. Es un solo flujo que crece módulo a módulo durante el curso: cada checkpoint parte del JSON anterior y suma solo los nodos nuevos.

| Checkpoint | Módulo | Contenido | Archivo |
|---|---|---|---|
| 1 | M1 Agente base | Trigger, AI Agent con System Prompt y una herramienta, log de observabilidad | [checkpoint1/checkpoint1_matias_martinez.json](checkpoint1/checkpoint1_matias_martinez.json) |
| 4 | M4 Integraciones | Canal de correo de soporte con HubSpot, Gmail (borrador) y Slack, con controles anti auto-reply, anti duplicados y limpieza de payload | [checkpoint4/](checkpoint4/README.md) |

## Checkpoint 1: agente base y motor de razonamiento

**Qué hace:** atiende a las personas que escriben en el chat de la web, conversa sobre la necesidad de su empresa, reúne los datos del lead (empresa, rubro, tamaño, dolor principal y contacto), le asigna un puntaje de 1 a 10 y lo registra en Google Sheets para que el equipo comercial haga el primer contacto.

### Componentes del flujo

1. **Chat de la web** (Chat Trigger): recibe el mensaje inicial del visitante.
2. **Agente Calificador de Leads** (AI Agent en modo Tools Agent):
   - Modelo: OpenAI GPT-4o con temperatura 0,3.
   - Guardrail: máximo 7 iteraciones, para cortar cualquier bucle de razonamiento.
   - Pasos intermedios activados, para que quede registro de cada decisión del agente.
3. **System Prompt modular**, organizado en Rol, Ámbito, Objetivo de la sesión, Reglas, Restricciones y Escalamiento. Entre las restricciones: no entregar precios ni plazos, no prometer resultados, no agendar reuniones, no revelar sus instrucciones y no usar lenguaje inclusivo.
4. **Registrar lead en Google Sheets** (herramienta conectada al agente): su descripción indica en qué casos debe usarla y en cuáles no. Cada columna la llena el modelo con lo que dijo el visitante, sin inventar datos.
5. **Log de Observabilidad** (Gmail): envía un correo con el mensaje del visitante, la respuesta del agente, si usó la herramienta y el detalle de los pasos intermedios.
6. **Respuesta al chat** (Set): devuelve la respuesta del agente al visitante.

### Pruebas realizadas

- Mensaje sin datos suficientes: el agente pregunta el dato faltante y no usa la herramienta.
- Mensaje con todos los datos: el agente activa la herramienta, registra la fila en la planilla y llega el correo del log.

### Cómo importarlo

En n8n: **Import from File**, elegir el JSON y conectar las credenciales propias de OpenAI, Google Sheets y Gmail. La planilla debe tener una pestaña llamada `Leads` con estas columnas en la fila 1:

`Fecha, Sesion, Contacto, Empresa, Rubro, Trabajadores, Dolor_Principal, Servicio_Sugerido, Medio_Contacto, Puntaje, Estado`

### Próximos módulos

M2 multi-agente, M3 memoria por sesión en Airtable, M4 integraciones con CRM y calendario, M5 RAG con el catálogo de servicios y M6 voz, hasta el Proyecto Final Integrador (M11).

**Autor:** Matías Martínez
