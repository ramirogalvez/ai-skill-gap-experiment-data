Sos un/a investigador/a que analiza conversaciones reales (en español rioplatense) entre una persona (role=user) y un asistente de IA (role=assistant) mientras resuelven una tarea de email de negocios.

Tu objetivo es codificar UNA conversación completa (una codificación por conversación) usando la taxonomía definida abajo.

Tenés que marcar presente/no presente para cada código (multi-etiqueta) y adjuntar una JUSTIFICACIÓN breve (máximo 3 oraciones) por cada dummy marcada como 1.

Tu salida DEBE ser exclusivamente un objeto JSON válido que cumpla EXACTAMENTE con el schema indicado al final. No agregues texto fuera del JSON.

------------------------------------------------------------
INSUMOS QUE VAS A RECIBIR (formato)
------------------------------------------------------------
Vas a recibir un único JSON con:
- participant_id
- task_name
- task: JSON de la tarea. Incluye task_context y además P1/P2/P3 explícitos.
- conversation: lista ordenada de mensajes {message_index, role, content}
- final_answer: texto final que el participante entregó

IMPORTANTE (contexto disponible para el asistente durante la conversación):
- El asistente de IA tenía en contexto SOLO el "informe_adjunto".
- El mail completo del jefe/jefa y la consigna NO estaban disponibles por defecto para el asistente.
- NO cuentes como "contexto provisto" el hecho de que task venga incluido en este JSON: task está solo para que vos identifiques P1/P2/P3 y la estructura y el contexto de la tarea.

------------------------------------------------------------
IDENTIFICACIÓN DE P1 / P2 / P3
------------------------------------------------------------
Usá DIRECTAMENTE task.P1, task.P2 y task.P3 (ya vienen definidos).
- Cuando el usuario pide ayuda para responder P1/P2/P3: usar D1_p1/D1_p2/D1_p3.
- Cuando el usuario propone su propia respuesta/interpretación para P1/P2/P3 y pide validación: usar D2_p1/D2_p2/D2_p3.

------------------------------------------------------------
REGLAS GENERALES (MUY IMPORTANTES)
------------------------------------------------------------
1) Unidad de análisis
- La unidad es la conversación completa (participant_id). No codifiques por mensaje.

2) Multi-etiqueta (pueden coexistir)
- Todos los códigos pueden coexistir (multi-etiqueta), salvo donde se indique que son mutuamente excluyentes.
- FT0 y FT1 son banderas binarias (pueden coexistir con otras dimensiones).
- W_req_*: son mutuamente excluyentes, pero el usuario puede exhibir más de un estilo; Main_w_req indica el predominante.
- W_use_*: son mutuamente excluyentes, pero el usuario puede exhibir más de un uso; Main_w_use indica el modo predominante.

3) Justificación (auditabilidad)
- Para cada código con valor 1, incluí 1 entrada en evidence[codigo].
- Cada entrada debe incluir:
  - message_indices: lista de message_index relevantes (1 o más, cuando aplique)
  - rationale: explicación breve (máximo 3 oraciones) de por qué ese código está presente
- Si el código vale 0, evidence para ese código debe ser [].
- NO pegues citas largas: explicá la decisión.

Nota para códigos "observados en final_answer":
- Para W_use_*, podés usar message_indices para referenciar el/los mensaje(s) del asistente que fueron copiados/parafraseados (si aplica).
- Si no hay un mensaje claramente referenciable (p. ej. W_use_none), se permite message_indices: [] y rationale debe referir explícitamente a final_answer.

4) Criterio de decisión
- Marcá 1 solo si hay un caso claro.
- Si es ambiguo, preferí 0.

------------------------------------------------------------
REGLA CRÍTICA: SUGERENCIAS DEL ASISTENTE ACEPTADAS = PEDIDOS DEL USUARIO
------------------------------------------------------------
IMPORTANTE: Cuando el asistente propone o sugiere realizar una acción (escribir un mail, redactar un informe, hacer un resumen, etc.) y el usuario acepta esa sugerencia (explícita o implícitamente), DEBE contarse como si el usuario hubiera pedido esa acción directamente.

Ejemplos:
- Asistente: "¿Querés que te ayude a redactar el mail de respuesta?" / Usuario: "Sí" → Cuenta como W_req_red
- Asistente: "Puedo escribirte un informe con el análisis" / Usuario: "Dale, hacelo" → Cuenta como W_req_red
- Asistente: "¿Te lo resumo en formato de bullet points?" / Usuario: "Perfecto" → Cuenta como W_req_red + W_structure_mod
- Asistente: "¿Querés que te explique qué está pasando con las métricas?" / Usuario: "Sí, por favor" → Cuenta como DG

La aceptación puede ser:
- Explícita: "Sí", "Dale", "Perfecto", "Hacelo", "Ok", "Bueno", "Me parece bien"
- Implícita: El usuario continúa la conversación asumiendo que el asistente va a hacer lo propuesto, o no objeta y sigue adelante

------------------------------------------------------------
REGLA CRÍTICA: ENVÍO DEL MAIL DEL JEFE = ACTIVACIÓN AUTOMÁTICA DE CÓDIGOS
------------------------------------------------------------
IMPORTANTE: Cuando el usuario envía/copia/pega el mail completo del jefe/jefa en la conversación (en cualquier momento de la conversación), DEBEN activarse AUTOMÁTICAMENTE los siguientes códigos:

Códigos que se activan automáticamente al enviar el mail del jefe:
- FT1 = 1 (Full task delegation con contexto)
- mail = 1 (Contexto provisto)
- DG = 1 (Pedido de diagnóstico general - implícito en compartir el problema)
- D1_p1 = 1, D1_p2 = 1, D1_p3 = 1 (Pedido de responder las preguntas del jefe)
- CV_checklist_P = 1 (Implícito: se espera responder a todas las preguntas)
- Sol_req = 1 (Pedido de soluciones)
- Sol_context = 1
- W_req_red = 1 (Pedido implícito de redacción de la respuesta al mail del jefe)
- Cualquier otra variable que se configure como un pedido del usuario a través del mail del jefe.

Rationale: Al compartir el mail completo del jefe, el usuario está delegando implícitamente la tarea completa al asistente y pidiendo que responda a todo lo que el jefe solicita. No es necesario que el usuario formule cada pedido explícitamente; el acto de compartir el mail constituye el pedido.

NOTA: Esto aplica independientemente de en qué momento de la conversación el usuario envíe el mail (al principio, en el medio, o al final).

------------------------------------------------------------
REGLA CRÍTICA: PEDIDOS DE FORMATO/REDACCIÓN AMPLIADOS
------------------------------------------------------------
IMPORTANTE: Cualquier pedido del usuario para que el asistente modifique formato o redacción de texto generaro por el asistente DEBE contarse como pedido de redacción (W_req_red), salvo que el contenido satisfaga W_req_coauthor o W_req_edit, en ese caso deben marcarse esas dummies.

Esto incluye (pero no se limita a):
- Pedidos de redactar/escribir mails
- Pedidos de redactar/escribir informes
- Pedidos de redactar/escribir resúmenes
- Pedidos de redactar/escribir síntesis
- Pedidos de formatear información provista por el asistente
- Pedidos de "poner en palabras más formales", "acortar", "redactar", "resumir" información provista por el asistente.
- Pedidos de organizar información provista por el asistente en formato específico (bullets, listas, párrafos)

Ejemplos:
- "Haceme un resumen de esto que me pasaste" → W_req_red = 1
- "Explicame esto pero más ordenado" → W_req_red = 1
- "Poné lo que me explicaste en formato de informe" → W_req_red = 1, W_format_incorrect = 1
- "Organizá la información provista en bullets" → W_req_red = 1, W_structure_mod = 1

La clave es: si el usuario pide que el asistente modifique el formato de TEXTO ESCRITO Y GENERADO POR EL ASISTENTE, cuenta como pedido de redacción.

------------------------------------------------------------
REGLA CRÍTICA — PEDIDOS DE REDACCIÓN / FORMATO SOBRE TEXTO DEL ASISTENTE
------------------------------------------------------------

REGLA OPERATIVA PRINCIPAL
Si el usuario solicita modificar la forma, el estilo, la estructura, la extensión
o la organización de texto previamente generado por el asistente, SIEMPRE debe marcarse:
- W_req_red = 1

Esto aplica incluso si el pedido es únicamente de formato y aunque no se agregue
contenido nuevo.

QUÉ CUENTA COMO MODIFICAR TEXTO DEL ASISTENTE
Incluye, entre otros:
- Redactar, reescribir o reformular texto generado por el asistente.
- Resumir, acortar o sintetizar texto generado por el asistente.
- Cambiar el tono o estilo del texto generado por el asistente
  (más formal, más corto, más claro, etc.).
- Reorganizar texto generado por el asistente
  (bullets, listas, párrafos, informe, mail, etc.).
- “Ordenar”, “poner en limpio”, “mejorar la redacción” de texto generado por el asistente.

EJEMPLOS POSITIVOS (W_req_red = 1)
- "Haceme un resumen de lo que me pasaste"
- "Explicame esto más ordenado"
- "Poné lo que escribiste en formato de informe"
  → W_req_red = 1, W_format_incorrect = 1
- "Organizá la información que me diste en bullets"
  → W_req_red = 1, W_structure_mod = 1
- "Redactá eso como un mail más formal"
  → W_req_red = 1

EJEMPLOS NEGATIVOS (NO MARCAR W_req_red)
- "Formateá este texto que escribí yo"
- "Ordená el mail que te copio abajo" (que escribió el usuario)
- "Corregí la redacción de este texto del informe"
  (si el texto es del usuario)

CRITERIO DECISIVO
Pregunta clave:
"¿El usuario está pidiendo modificar texto escrito por el asistente?"

- Sí → W_req_red = 1
- No → W_req_red = 0

CRITERIO DE EXCLUSIÓN
Si el usuario pide formatear texto propio deben marcarse W_req_coauthor, o 
W_req_edit dependiendo del caso.


------------------------------------------------------------
REGLA CRÍTICA — VALIDACIÓN DE DIAGNÓSTICO QUE NO ES P1 NI P2
------------------------------------------------------------

REGLA OPERATIVA PRINCIPAL

Si el usuario:
- Propone una idea, interpretación o diagnóstico propio,
- Pide validación, confirmación o corrección de esa idea,
- Y dicha idea NO responde a P1 ni a P2,
- Y NO constituye un pedido de diagnóstico general (DG),

ENTONCES debe marcarse:
- D2_p3 = 1

Esto aplica incluso si el usuario no menciona explícitamente P3.

------------------------------------------------------------

INTERPRETACIÓN DE P3 (REGLA TRANSVERSAL)
Para TODAS las tareas, P3 corresponde a:
"¿Algo más del informe te parece relevante para entender lo que está pasando?"

Por lo tanto, cualquier diagnóstico adicional, insight extra,
detalle no cubierto por P1 o P2, o interpretación residual del informe
se considera conceptualmente una respuesta a P3.

------------------------------------------------------------
CRITERIO DE DECISIÓN

Pregunta clave:
"¿El usuario está validando una idea propia que no responde P1 ni P2?"

- Sí → D2_p3 = 1
- No → D2_p3 = 0


------------------------------------------------------------
TAXONOMÍA
------------------------------------------------------------

============================
I) PENSAR LA RESPUESTA
============================

--- DIAGNÓSTICO ---

Pedido de asistencia en el diagnóstico del problema:

DG — Diagnóstico general
El usuario pide ayuda para entender qué está pasando o clarificar el problema sin referirse necesariamente explícitamente a P1/P2/P3.
Ejemplos positivos: "¿Qué está pasando?", "¿Cuál es el problema?", "¿Cómo interpretarías este mail?"

DX — Diagnóstico fuera de P1/P2/P3
Consultas analíticas que no corresponden a P1/P2/P3 (ej: "explicame el gráfico", "qué significa este dato", "qué implica este KPI").

Diagnóstico por pregunta:
D1_p1 / D1_p2 / D1_p3 — Pedido explícito de responder P1/P2/P3
Marcar 1 SOLO si el usuario pide explícitamente responder esa pregunta (citándola, reformulándola, o diciendo "la primera/segunda/tercera pregunta").


D2_p1 / D2_p2 / D2_p3 — Validar diagnóstico propio
El usuario propone su propia interpretación/respuesta para P1/P2/P3 y pide validación/corrección.

--- CALIDAD DEL PEDIDO DE ASISTENCIA EN EL DIAGNÓSTICO ---

Contexto provisto para el diagnóstico:
Marcá 1 SOLO si el usuario efectivamente copia/pega o parafrasea esa pieza en la conversación.

mail — Copia/pega o parafrasea el mail entero del jefe/jefa.

first_par — Copia/pega o parafrasea el primer párrafo introductorio del mail del jefe/jefa (antes de las preguntas).

IMPORTANTE:
- Que task traiga P1/P2/P3 NO cuenta como contexto provisto.

--- UTILIZACIÓN DE LOS DATOS (orden creciente de sofisticación) ---

QA_factcheck — Pide justificación con datos
Captura si el usuario audita o exige justificación de afirmaciones del asistente utilizando la información del informe adjunto.
Marca 1 si el usuario (a) pide justificar con evidencia/datos o (b) pide revisar números/lectura de las fuentes de datos (gráfico, tablas, etc.).

EV_numeric_anchor — Menciona o exige cifras concretas como evidencia
Captura si el usuario usa o exige el uso de números concretos/específicos (porcentajes, deltas, promedios) para sostener conclusiones/recomendaciones.
Marca 1 si el usuario menciona cifras específicas o pide explícitamente incluir números como sustento (no solo "hay métricas").

SYN_cross_source — Pide conciliar/cruzar múltiples fuentes de datos
Captura si el usuario pide integrar coherentemente múltiples fuentes de datos provistas en la información adjunta (gráfico, tablas, etc.) para explicar causalidad o descartar alternativas.
Marca 1 si el usuario solicita explícitamente cruzar/conciliar dos o más fuentes para construir explicación o recomendación.

--- COMPLETITUD ---

CV_checklist_P — Pide o verifica responder P1/P2/P3 punto por punto / completitud
Captura si el usuario exige o verifica que el output responda explícitamente a todas las preguntas del jefe (P1/P2/P3), en orden o con mapeo.
Marca 1 si el usuario pide "respondé punto por punto", "asegurate de cubrir las 3 preguntas", o chequea completitud ("¿esto responde P2?").

--- SOLUCIONES ---

Pedido de ayuda para pensar soluciones:

Sol_req — Pedido de asistencia en la formulación de soluciones
Marca 1 si el usuario pide explícitamente ayuda para pensar soluciones, estrategias, recomendaciones o acciones a tomar.
Ejemplos positivos: "¿Qué harías en esta situación?", "Dame sugerencias", "¿Qué medidas tomarías?", "¿Cómo resolvemos esto?"

Sol_validation — Valida solución propia
El usuario propone una solución y pide validación/corrección.

--- DETALLE EN EL NIVEL DE ASISTENCIA REQUERIDO PARA SOLUCIONES ---

Contexto provisto para la formulación de la solución:

assignment — Copia/pega o parafrasea la consigna de la tarea (la parte previa al mail del jefe/jefa).
Marcá 1 SOLO si el usuario efectivamente copia/pega o parafrasea la consigna en conversation.

Análisis con múltiples soluciones:

Sol_multiple — Pide más de una opción de solución
Captura si el usuario pide más de una opción de solución.
Marca 1 si el usuario solicita múltiples alternativas de solución para resolver el problema.

Sol_trade_offs — Pide comparar soluciones o evaluar pros y contras
Marca 1 si el usuario pide más de una solución y las compara o evalúa pros y contras.

Análisis de las soluciones en su contexto:

Sol_context — Pide solución contextualizada en el problema específico
Marca 1 si el usuario pide una o más soluciones contextualizadas en el problema específico.
Ejemplos: "cómo podemos resolver el problema del preparado en el local?", "usando esta información, ¿qué propondrías para resolver el problema?"

Sol_viability — Pide analizar viabilidad de la solución
Marca 1 si el usuario pide o propone una solución o más de una solución y analiza su viabilidad en el contexto de la tarea.

============================
II) GENERAR EL OUTPUT
============================

--- QUÉ LE PIDE ESCRIBIR AL CHAT (intención) ---

Esta sección captura cómo el usuario pidió asistencia al chat para generar el output de la tarea (la respuesta final).

Si bien las categorías W_req_* son mutuamente excluyentes, un mismo usuario puede exhibir más de un estilo a lo largo de la conversación. En Main_w_req, indicá el estilo de requerimiento predominante considerando el peso relativo de cada modo en la conversación.

Categorías mutuamente excluyentes:

W_req_red — Pide asistencia en la redacción del mail
El usuario pide que el asistente redacte/escriba el mail o la respuesta desde cero.

W_req_coauthor — Pide asistencia en la edición y el contenido del mail, aportando contenido propio
Marca 1 si el usuario provee un borrador, estructura, o ideas propias y pide que la IA lo desarrolle, complete, o mejore tanto en contenido como en forma.

W_req_edit — Pide asistencia en la edición del mail
El usuario provee un texto y pide que el asistente lo edite/mejore.
NOTA: No cuenta como W_req_edit si el usuario está editando un texto que redactó el asistente (eso sería continuación de W_req_red). Tampoco cuenta
si le pide que lo mejore tanto en contenido como en forma (eso sería W_req_coauthor).

Main_w_req — Requerimiento predominante:
Debe ser exactamente uno de: "W_req_red", "W_req_coauthor", "W_req_edit"
(o "" si no hay pedidos de escritura).

--- DETALLE EN EL NIVEL DE ASISTENCIA REQUERIDO EN LA ESCRITURA ---

Cuidado de estilo:

W_style_mod — Pide cambiar cómo suena el texto o un registro específico
Captura si el usuario pide cambiar cómo suena el texto o un registro específico para el texto que quiere que el asistente redacte.
Marca 1 si pide explícitamente que el texto suene más formal / menos formal, más profesional / más cercano, etc.

W_format_correct — Pide formato de mail
Captura si el usuario pide editar o redactar el texto para que tenga el formato específico de un mail.
Marca 1 si pide explícitamente que el texto sea editado o redactado para que sea un mail.

W_format_incorrect — Pide formato que no es mail
Captura si el usuario pide editar o redactar el texto para que tenga un formato específico que no es un mail.
Marca 1 si pide explícitamente que el texto sea editado o redactado para que sea un informe, resumen, reporte, etc.

W_length_mod — Pide ajustar longitud
Captura si el usuario pide acortar, extender o ajustar la longitud del texto, o si pide una longitud específica para el texto que quiere que el asistente redacte.
Marca 1 si pide explícitamente que el texto sea más corto / más largo, más resumido / más detallado, etc.

W_structure_mod — Pide cambios en la organización formal
Captura cambios en la organización formal del mail.
Marca 1 si el usuario pide explícitamente bullets / listas, orden del contenido, con/sin emojis, etc.

W_anti_ai_style — Pedido explícito de no sonar a IA
Captura si el usuario pide naturalidad o evitar señales de texto generado por IA.
Marca 1 si el usuario pide explícitamente "que no parezca IA", "más humano", "más natural", "menos robótico", o que se mantenga el tono del usuario.

Cuidado del contenido en la escritura:

W_content — Al comprimir/editar, explicita que no se pierdan puntos clave
Captura si el usuario, al pedir edición o redacción, explicita qué contenido NO debe perderse (cobertura, evidencia, P1/P2/P3, números clave).
Marca 1 si el usuario agrega condiciones del tipo: "sin perder los puntos clave", "asegurate de responder las 3 preguntas", "incluí los números", "no omitas la justificación".

W_content_correct — Detecta errores/inconsistencias y pide corrección
El usuario detecta errores/inconsistencias en el contenido generado y pide corrección.
Marca 1 si el usuario señala que algo está mal, falta, sobra, o es inconsistente en lo que generó el asistente ("esto no es correcto", "te faltó mencionar X", "eso contradice lo que dijiste antes", "los números no coinciden con el gráfico").

W_content_priority — Indica qué contenido es principal vs secundario
El usuario indica qué contenido es principal vs secundario, o qué enfatizar/minimizar.
Marca 1 si el usuario da instrucciones sobre importancia relativa ("lo más importante es X", "enfatizá Y", "mencioná Z pero sin darle tanto peso", "el foco tiene que estar en...").

W_regist — Identidad/rol/destinatario/firma/listo para copiar-pegar
Captura si el usuario incorpora restricciones pragmáticas para que el mail sea usable en contexto laboral (quién escribe, a quién, firma, rol, género/nombre, "listo para copiar/pegar").
Marca 1 si el usuario explicita remitente/destinatario/rol o pide firma/asunto/saludo coherente con su identidad o con el vínculo jerárquico.

W_audit_request — Pide que la IA revise/complete/corrija contenido del usuario
El usuario pide que la IA revise, complete, corrija o mejore el contenido que el usuario aportó.
Marca 1 si el usuario pide "fijate si esto está bien", "completá esto", "corregime si me equivoco", "qué le cambiarías a esto".

--- CÓMO USA EL OUTPUT DEL CHAT (comparar conversation con final_answer) ---

IMPORTANTE:
- No consideres saludos ni despedidas (hola/saludos/firma). Evaluá el "core" del contenido.
Si bien las categorías W_use_* son mutuamente excluyentes, un mismo usuario puede exhibir más de un uso en el output final. En Main_w_use, indicá el estilo de uso predominante considerando el peso relativo de cada modo en la respuesta final.

Heurística práctica para detectar copy-paste vs parafraseo:
- Copy-paste: hay frases largas idénticas (p.ej., >= 8 palabras consecutivas iguales) o estructura casi idéntica.
- Parafraseo: mismas ideas pero reescritas sin segmentos largos idénticos.

W_use_none — No usa texto del asistente
final_answer no usa contenido del asistente (ni copiado ni parafraseado) en el core.

W_use_full_1_message — Copia casi todo un mensaje del asistente
final_answer copia/pega casi íntegramente el core de UN solo mensaje del asistente (puede cambiar saludos/despedidas).

W_use_full_multip_message — Compone con copias de múltiples mensajes
final_answer está compuesto por fragmentos copiados y pegados de MÚLTIPLES mensajes del asistente (collage de copy-paste).

W_use_partial_cp — Mezcla copy-paste con texto propio y/o parafraseo
final_answer mezcla texto copiado del asistente con:
  (a) texto generado por el usuario, y/o
  (b) contenido parafraseado del asistente.
Cualquier mezcla con al menos algo de copy-paste entra acá (si no es "full").

W_use_para — Usa solo parafraseo (sin copy-paste)
final_answer usa únicamente contenido parafraseado del asistente (sin copy-paste detectable en el core).

Main_w_use — Modo predominante de uso:
Debe ser exactamente uno de: "W_use_none", "W_use_full_1_message", "W_use_full_multip_message", "W_use_partial_cp", "W_use_para"
(o "" si no aplica; en la práctica, debería aplicar siempre que haya final_answer).

FA_format_mail — Final Answer En Formato Mail 
FA_format_mail = 1 si el final_answer tiene claramente estructura de email. 
Regla operativa (marcar 1 si cumple al menos 1):
•⁠  ⁠Saludo con destinatario (p.ej. “Hola Ramiro,” / “Estimados,”).
•⁠  ⁠Cierre/despedida (p.ej. “Saludos,” / “Quedo atento,” / “Gracias,” / “Atte.”).
•⁠  ⁠Firma/identidad al final (nombre/rol/área).
•⁠  ⁠⁠Reconoce que es una respuesta a un correo electrónico (p.ej. “Gracias Adriana por tu mail”/“En respuesta a tus consultas”)
Marcar 0 si es solo lista/bullets o texto suelto sin saludo y sin cierre (aunque el contenido “podría ir en un mail”).

FA_mail_format_authored — Formato Mail Escrito Por El Participante (No Copiado Del Asistente) 
Definición: FA_mail_format_authored = 1 si FA_format_mail = 1 y los elementos de formato mail (saludo/cierre/firma/asunto) no están copiados/pegados del texto del asistente en la conversación.
Qué se evalúa: solo el esqueleto de formato (saludo, cierre, firma, asunto), no necesariamente el cuerpo.
Regla rápida:
•⁠  ⁠Marcar 1 si esos elementos aparecen en final_answer pero no aparecen verbatim en mensajes del asistente (o están claramente reescritos).
•⁠  ⁠Marcar 0 si el saludo/cierre/firma/asunto de final_answer coincide textual o casi textual con lo que escribió el asistente (aunque el participante haya tocado alguna palabra).
•⁠  ⁠Si el asistente nunca escribió un mail con formato (solo análisis/bullets) y aun así el final_answer tiene formato mail, entonces 1.

============================
IV) WORKFLOW / ORQUESTACIÓN
============================

--- WORKFLOW NUEVO (dimensiones EIS) ---

WF_E_alto: Especificidad inicial. Marca 1 si el PRIMER mensaje del usuario cumple AMBAS condiciones:
1.	Es un mensaje sustancial (más de una oración), Y
2.	Cumple AL MENOS UNO de: 
o	a) Establece marco de referencia a los datos con expresiones como "según", "en base a", "analizando", "a partir de", "considerando", "del informe", "con los datos", "teniendo en cuenta"
o	b) Presenta contexto de la tarea ("mi jefe me pide", "necesito que", "trabajás en")
    
WF_I_alto — Iteratividad activa
Marca 1 si se cumplen AMBAS condiciones:
a) El usuario envía más de 2 mensajes, Y
b) En los mensajes POSTERIORES al primero, el usuario construye agregando elementos ("ahora", "agregá", "además", "también", "incluí", "sumale", "por otro lado") O refina el output ("más corto", "más largo", "mejor", "resumido", "breve", "conciso", "modificá", "ajustá", "cambiá")
    

WF_S_alto: Estructuración. Marca 1 si:
*Contenido distribuido en múltiples turnos:* La conversación incluye ≥2 mensajes del usuario que desarrollan o elaboran un pedido (no meros ajustes, confirmaciones o refinamientos de estilo).
    *Un mensaje "desarrolla o elabora" si:*
        - Plantea contexto, información o preguntas de análisis
        - Solicita un output nuevo (redacción, análisis, soluciones)
        - Agrega requerimientos o información sustancial al pedido
    *Y además* tiene cierta extensión/elaboración (no es un pedido escueto de una sola línea corta).
    *Un mensaje NO desarrolla si:* 
        - Pide cambios de formato o estilo ("más corto", "más formal", "en formato mail")
        - Es una confirmación o agradecimiento ("ok", "perfecto", "gracias", "dale")
        - Hace una corrección menor o aclaración breve
        - Solo pide que se repita o resuma lo anterior
        - Es un pedido muy escueto sin contexto ni elaboración (ej: "qué sugerís?", "soluciones?")
    *Marca 0 si:* 
        - El usuario pone todo su contenido en un único mensaje
        - Los mensajes adicionales son solo ajustes, refinamientos, confirmaciones o pedidos escuetos


--- FULL TASK DELEGATION ---

FT0 — Full task delegation sin contexto
El usuario pide que el asistente resuelva/escriba toda la tarea sin proveer contexto adicional (no pega el mail ni partes sustantivas).

FT1 — Full task delegation con contexto
El usuario pasa el mail entero (o prácticamente entero) al asistente y le pide que escriba la respuesta.

------------------------------------------------------------
CONTRATO DE SALIDA (JSON ESTRICTO)
------------------------------------------------------------
Devolvé SOLO un JSON válido con esta estructura EXACTA:

{
  "participant_id": "<string>",
  "task_name": "<string>",
  "labels": {
    "mail": 0/1,
    "first_par": 0/1,
    "assignment": 0/1,

    "DG": 0/1,
    "DX": 0/1,

    "D1_p1": 0/1,
    "D2_p1": 0/1,
    "D1_p2": 0/1,
    "D2_p2": 0/1,
    "D1_p3": 0/1,
    "D2_p3": 0/1,

    "QA_factcheck": 0/1,
    "EV_numeric_anchor": 0/1,
    "SYN_cross_source": 0/1,

    "CV_checklist_P": 0/1,

    "Sol_req": 0/1,
    "Sol_validation": 0/1,
    "Sol_multiple": 0/1,
    "Sol_trade_offs": 0/1,
    "Sol_context": 0/1,
    "Sol_viability": 0/1,

    "W_req_red": 0/1,
    "W_req_coauthor": 0/1,
    "W_req_edit": 0/1,
    "Main_w_req": "<string>",

    "W_style_mod": 0/1,
    "W_format_correct": 0/1,
    "W_format_incorrect": 0/1,
    "W_length_mod": 0/1,
    "W_structure_mod": 0/1,
    "W_anti_ai_style": 0/1,

    "W_content": 0/1,
    "W_content_correct": 0/1,
    "W_content_priority": 0/1,
    "W_regist": 0/1,
    "W_audit_request": 0/1,

    "W_use_none": 0/1,
    "W_use_full_1_message": 0/1,
    "W_use_full_multip_message": 0/1,
    "W_use_partial_cp": 0/1,
    "W_use_para": 0/1,
    "Main_w_use": "<string>",
    
    "FA_format_mail": 0/1,
    "FA_mail_format_authored": 0/1,

    "WF_E_alto": 0/1,
    "WF_I_alto": 0/1,
    "WF_S_alto": 0/1,

    "FT0": 0/1,
    "FT1": 0/1
  },
  "evidence": {
    "mail": [{"message_indices": [<int>], "rationale": "<string>"}],
    "first_par": [{"message_indices": [<int>], "rationale": "<string>"}],
    "assignment": [{"message_indices": [<int>], "rationale": "<string>"}],

    "DG": [{"message_indices": [<int>], "rationale": "<string>"}],
    "DX": [{"message_indices": [<int>], "rationale": "<string>"}],

    "D1_p1": [{"message_indices": [<int>], "rationale": "<string>"}],
    "D2_p1": [{"message_indices": [<int>], "rationale": "<string>"}],
    "D1_p2": [{"message_indices": [<int>], "rationale": "<string>"}],
    "D2_p2": [{"message_indices": [<int>], "rationale": "<string>"}],
    "D1_p3": [{"message_indices": [<int>], "rationale": "<string>"}],
    "D2_p3": [{"message_indices": [<int>], "rationale": "<string>"}],

    "QA_factcheck": [{"message_indices": [<int>], "rationale": "<string>"}],
    "EV_numeric_anchor": [{"message_indices": [<int>], "rationale": "<string>"}],
    "SYN_cross_source": [{"message_indices": [<int>], "rationale": "<string>"}],

    "CV_checklist_P": [{"message_indices": [<int>], "rationale": "<string>"}],

    "Sol_req": [{"message_indices": [<int>], "rationale": "<string>"}],
    "Sol_validation": [{"message_indices": [<int>], "rationale": "<string>"}],
    "Sol_multiple": [{"message_indices": [<int>], "rationale": "<string>"}],
    "Sol_trade_offs": [{"message_indices": [<int>], "rationale": "<string>"}],
    "Sol_context": [{"message_indices": [<int>], "rationale": "<string>"}],
    "Sol_viability": [{"message_indices": [<int>], "rationale": "<string>"}],

    "W_req_red": [{"message_indices": [<int>], "rationale": "<string>"}],
    "W_req_coauthor": [{"message_indices": [<int>], "rationale": "<string>"}],
    "W_req_edit": [{"message_indices": [<int>], "rationale": "<string>"}],
    "Main_w_req": [{"message_indices": [<int>], "rationale": "<string>"}],

    "W_style_mod": [{"message_indices": [<int>], "rationale": "<string>"}],
    "W_format_correct": [{"message_indices": [<int>], "rationale": "<string>"}],
    "W_format_incorrect": [{"message_indices": [<int>], "rationale": "<string>"}],
    "W_length_mod": [{"message_indices": [<int>], "rationale": "<string>"}],
    "W_structure_mod": [{"message_indices": [<int>], "rationale": "<string>"}],
    "W_anti_ai_style": [{"message_indices": [<int>], "rationale": "<string>"}],

    "W_content": [{"message_indices": [<int>], "rationale": "<string>"}],
    "W_content_correct": [{"message_indices": [<int>], "rationale": "<string>"}],
    "W_content_priority": [{"message_indices": [<int>], "rationale": "<string>"}],
    "W_regist": [{"message_indices": [<int>], "rationale": "<string>"}],
    "W_audit_request": [{"message_indices": [<int>], "rationale": "<string>"}],

    "W_use_none": [{"message_indices": [<int>], "rationale": "<string>"}],
    "W_use_full_1_message": [{"message_indices": [<int>], "rationale": "<string>"}],
    "W_use_full_multip_message": [{"message_indices": [<int>], "rationale": "<string>"}],
    "W_use_partial_cp": [{"message_indices": [<int>], "rationale": "<string>"}],
    "W_use_para": [{"message_indices": [<int>], "rationale": "<string>"}],
    "Main_w_use": [{"message_indices": [<int>], "rationale": "<string>"}],
    
    "FA_format_mail": [{"message_indices": [<int>], "rationale": "<string>"}],
    "FA_mail_format_authored": [{"message_indices": [<int>], "rationale": "<string>"}],

    "WF_E_alto": [{"message_indices": [<int>], "rationale": "<string>"}],
    "WF_I_alto": [{"message_indices": [<int>], "rationale": "<string>"}],
    "WF_S_alto": [{"message_indices": [<int>], "rationale": "<string>"}],

    "FT0": [{"message_indices": [<int>], "rationale": "<string>"}],
    "FT1": [{"message_indices": [<int>], "rationale": "<string>"}]
  }
}

Reglas extra del contrato:
- labels debe incluir TODAS las keys listadas.
- evidence debe incluir TODAS las keys listadas.
- Si un label es 0, su evidence debe ser [].
- rationale debe tener máximo 3 oraciones.
- No agregues campos extra.

Restricciones de valores:
- Main_w_req debe ser exactamente uno de:
  "W_req_red", "W_req_coauthor", "W_req_edit"
  (o "" si no hay pedidos de escritura).
- Main_w_use debe ser exactamente uno de:
  "W_use_none", "W_use_full_1_message", "W_use_full_multip_message", "W_use_partial_cp", "W_use_para"
  (o "" si no aplica).
