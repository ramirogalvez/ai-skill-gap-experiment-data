# Rubricas detalladas por tarea
[turn1]
Leé el archivo `Tarea electrodomesticos.docx` adjuntado al proyecto actual. Tenemos la siguiente grading guide genérica:

------------------------------------------------------------
DETALLE DE CADA SUB-DIMENSIÓN
------------------------------------------------------------

[1] DIAGNÓSTICO (diagnosis_q1, diagnosis_q2, diagnosis_q3) – 0 a 2 puntos cada uno

Evaluan si la persona responde bien a cada una de las tres preguntas específicas planteadas por el jefe / jefa en el mail. Para cada pregunta:

- 2 puntos (BUENO):
  - Responde de forma directa y clara a la pregunta.
  - La conclusión principal es correcta según los datos del informe.
  - Usa al menos una referencia concreta al informe (números, comparaciones, períodos, sucursales o productos específicos).
  - No hay errores graves de lectura de tablas/gráficos que cambien el sentido (no confunde más con menos, ni Norte con Sur, etc.).

- 1 punto (PARCIAL):
  - Responde en la dirección correcta, pero de forma incompleta, vaga o poco fundamentada.
  - Puede mezclar algún error menor o una interpretación confusa, pero la idea central sigue siendo razonablemente útil.
  - Puede mencionar datos sin interpretarlos bien, o interpretar bien sin apoyar con datos concretos.

- 0 puntos (MAL o NO RESPONDE):
  - No responde realmente a la pregunta (se va por las ramas o repite el enunciado).
  - La conclusión central es incorrecta y contradice claramente la información del informe.
  - O comete errores graves de lectura / cálculo que invierten el sentido de los datos o confunden completamente qué está pasando (por ejemplo, afirmar que hay más pedidos donde los datos muestran claramente menos).
  - Solo copia o parafrasea el informe sin dar una conclusión diagnóstica.

Si ves un error cuantitativo o de interpretación tan grande que invalida el razonamiento, asigná 0 en esa pregunta, aunque aparezcan palabras aparentemente “correctas”.

[2] SOLUCIÓN (solution_root_cause, solution_specificity, solution_realism)

- solution_root_cause (0 o 1 punto):
  - 1: Las acciones propuestas atacan claramente la causa raíz del problema, según lo que muestran los datos (por ejemplo, intervienen sobre el cuello de botella correcto, el segmento relevante de clientes, el producto problemático, etc.).
  - 0: Las acciones van dirigidas a algo que NO es la causa raíz (por ejemplo, gastar en marketing cuando el problema es operativo) o son tan vagas que no queda claro qué causa abordan.

- Regla de pasa/no pasa:
  - Si solution_root_cause = 0, entonces:
    - solution_specificity = 0
    - solution_realism = 0
    - En la práctica, la persona se lleva 0 puntos en la sección de solución, aunque haya texto con ideas.

- solution_specificity (0, 1 o 2 puntos):
  - 2: Propuesta concreta y accionable. Se indica qué hacer, quién, cómo y/o cuándo con cierto detalle (por ejemplo, “agregar un empleado en plancha en horario pico los fines de semana”, “probar una campaña por mail solo en el segundo semestre con aire acondicionado”, etc.).
  - 1: Idea correcta en la dirección general (“capacitar al personal”, “revisar el proceso de preparación”), pero con poco detalle operativo.
  - 0: Muy vaga o inexistente (“mejorar el servicio”, “hacer algo con marketing”) sin contenido accionable.

- solution_realism (0 o 1 punto):
  - 1: Las acciones parecen factibles dado el contexto: cambios en horarios, personal, promociones, negociación razonable con proveedores, pruebas a pequeña escala, etc.
  - 0: Propuestas claramente irreales o desproporcionadas (cambios de política nacional, reestructuraciones gigantescas, inversiones masivas incompatibles con un negocio de este tamaño, etc.).

[3] ESCRITURA – cuatro subdimensiones

(3.1) spelling_grammar (0–2):
- 2: Ortografía y gramática casi siempre correctas. Algún error menor (tildes, alguna concordancia) que no molesta.
- 1: Errores frecuentes pero el texto sigue siendo comprensible sin demasiado esfuerzo.
- 0: Errores muy frecuentes o graves que dificultan seriamente la comprensión (frases sin verbo, mezcla caótica, “lenguaje chat” muy pesado).

(3.2) clarity (0–3):
- 3: Mensaje muy claro. Cada idea se entiende sin ambigüedad; referencias a datos y sucursales/productos son explícitas.
- 2: En general claro, aunque haya alguna frase confusa o un poco enredada.
- 1: Bastante confuso; hay que releer varias veces para entender; se mezclan ideas y referencias.
- 0: Tan confuso que no se entiende bien qué diagnóstico o solución propone.

(3.3) organization (0–4):
- 4: Excelente organización. Tiene saludo, cuerpo y cierre de mail; separa diagnóstico y solución; aborda las preguntas en un orden lógico; usa párrafos o señales (“Primero…”, “En segundo lugar…”).
- 3: Buena organización general, aunque mezcle algo diagnóstico y solución o no separe tan claro las preguntas.
- 2: Organización aceptable pero des prolija: mucho texto en bloque, mezcla de temas, pero igual se puede encontrar cada respuesta.
- 1: Organización pobre; ideas muy mezcladas; difícil seguir el hilo, aunque con esfuerzo se reconstruye.
- 0: Casi sin estructura; lista caótica de frases, sin forma de mail, imposible distinguir bien qué parte responde a qué.

(3.4) tone_register (0–1):
- 1: Tono respetuoso y profesional, adecuado para escribirle a un jefe o dueña en Argentina (puede usar “vos” o “usted”, pero debe ser coherente y educado).
- 0: Tono inapropiado (demasiado coloquial, grosero, burlón, con insultos, exceso de emojis o abreviaturas tipo chat, etc.).

------------------------------------------------------------
REGLAS ESPECIALES
------------------------------------------------------------

- Si la respuesta parece claramente muy corta (por ejemplo, una o dos frases mínimas) marcá `too_short_for_writing = true` en flags. Sin embargo, la regla formal es: si longitud < 200 caracteres Y contenido_total = 0 ⇒ escritura_total = 0.
- Si la respuesta está casi completamente fuera de tema (no menciona nada relacionado con la situación, los datos o las preguntas), marcá `off_topic = true` y poné 0 en las tres preguntas de diagnóstico y en solution_root_cause.
- Usá todo el rango de puntuaciones. No tengas miedo de dar 0, 1 o los máximos donde corresponda.

Expandí esta grading guide al caso específico de `Tarea electrodomesticos.docx`, en particular para la sección diagnóstico. Cuáles son los hechos clave que debería mencionar la respuesta a cada pregunta? Cuáles son los potenciales confusores?


[turn2]
[pego el grafico de la tarea]
Analize el gráfico y reescribe la rúbrica de acuerdo con los nuevos datos provistos

[turn3]
Ahora generá una rúbrica similarmente detallada para solution_root_cause, solution_specificity, solution_realism

