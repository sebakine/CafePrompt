# Prompts texto-texto — CafePrompt (versión final, iteración 2)

Todos los prompts se ejecutan con **Google Gemini** (`gemini-3.5-flash-lite`, capa gratuita) desde el notebook `CafePrompt_POC.ipynb`.
Las llaves `{...}` son variables que el notebook reemplaza con los datos de cada lote (plantillas parametrizadas); las llaves dobles `{{ }}` son llaves literales escapadas para `str.format()`.
Las respuestas reales están en [`../outputs/cache_respuestas.json`](../outputs/cache_respuestas.json) (iteración 2) y [`../outputs/iteracion_1/`](../outputs/iteracion_1/) (iteración 1, incluye el texto exacto de los prompts v1).

| Prompt | Técnicas | Temperatura | Salida |
|---|---|---|---|
| P0 Línea base | zero-shot sin estructura | 0,7 | texto libre |
| P1 Ficha sensorial | rol + few-shot + JSON + delimitadores + restricciones | 0,3 | JSON |
| P2 Copy Instagram | rol + zero-shot + restricciones + encadenamiento (usa P1) | 0,8 | JSON |
| P3 Meta-prompt de imagen | rol + razonamiento guiado + plantilla visual + meta-prompting | 0,3 | JSON |
| P4 Guion de audio | rol + restricciones para TTS + encadenamiento (usa P1) | 0,5 | texto plano |
| P5 Rúbrica | LLM-as-judge + contexto de uso + JSON | 0 | JSON |

## Cambios de la iteración 1 a la iteración 2

| Prompt | Cambio | Motivo (falla detectada en la iteración 1) |
|---|---|---|
| P1 | Extensión en oraciones + rango de palabras + estructura origen → proceso → taza → invitación | 2 de 3 descripciones extendidas bajo 80 palabras |
| P2 | Hashtags CamelCase sin tildes ni duplicados; no copiar oraciones de P1; ganchos distintos | Hashtags con tildes/duplicados y variantes que copiaban la ficha |
| P3 | 55-75 palabras en frases cortas; exclusiones solo en el negative prompt; "plain unbranded" | Prompts de más de 90 palabras y "no text, no logos" dentro del prompt positivo |
| P4 | 5-6 oraciones, 60-80 palabras | 2 de 3 guiones sobre 85 palabras |
| P5 | Contexto de uso ("se publica tal cual") y definición estricta de dato inventado | La rúbrica v1 no penalizaba textos con varias opciones ni métodos fuera de la ficha |

---

## Instrucción de sistema (rol) — común a P1–P4

```text
Eres «CaféPrompt», redactor senior de marketing gastronómico especializado en café de especialidad,
con formación de catador (protocolo SCA) y diez años de experiencia en tostadurías de Latinoamérica.
Trabajas para Café Altura, tostaduría y cafetería de especialidad en Santiago de Chile.
Tono de marca: cercano, experto y cálido; educa sin sonar técnico; español de Chile neutro, sin modismos.
Público: adultos de 25 a 45 años curiosos por el café, que aún no dominan términos de cata.
Reglas permanentes:
1. Usa ÚNICAMENTE los datos de la ficha técnica entregada; si un dato no está, omítelo. Nunca inventes notas, premios ni cifras.
2. Traduce la jerga técnica (proceso, altitud, variedad) a experiencias sensoriales que un cliente entienda.
3. Está prohibido: voseo o modismos argentinos, garabatos, afirmaciones de salud, superlativos absolutos ('el mejor del mundo'), datos no presentes en la ficha.
4. Escribe en español de Chile neutro, con tuteo ("prueba", "descubre"), nunca voseo.
```

## P0 — Línea base (prompt ingenuo)

```text
Describe este café para venderlo en mi cafetería: {pais}, {region}, {variedad}, {proceso}, {altitud}, notas: {notas}, {puntaje_sca} puntos.
```

Ejemplo instanciado (lote Etiopía):

```text
Describe este café para venderlo en mi cafetería: Etiopía, Guji, Oromía, Heirloom etíope (variedades locales), Natural (secado en camas africanas por 21 días), 1.950–2.100 msnm, notas: arándano, frutilla madura, chocolate de leche, jazmín, 87 puntos.
```

## P1 — Ficha sensorial para clientes (few-shot + JSON)

```text
### TAREA ###
Transforma la ficha técnica de un lote de café en una ficha sensorial para clientes de la cafetería.

### FORMATO DE SALIDA ###
Responde solo con un objeto JSON con estas claves exactas:
nombre_comercial (máx. 6 palabras), descripcion_menu (2 oraciones, entre 30 y 45 palabras),
descripcion_extendida (5 o 6 oraciones, entre 85 y 115 palabras: origen → proceso → taza → invitación),
notas_para_cliente (lista con una entrada por cada nota de cata: {{"nota", "analogia"}}; analogías con alimentos conocidos en Chile),
intensidad_1a5 (entero), acidez_1a5 (entero), preparacion_recomendada (una oración con receta),
maridaje (un alimento o pastelería habitual en Chile), ideal_para (una oración).

### EJEMPLO ###
Ficha:
"""
{ejemplo_ficha}
"""
Salida:
{ejemplo_salida}

### FICHA A TRANSFORMAR ###
"""
{ficha}
"""
Salida:
```

Ejemplo few-shot utilizado (café de Kenia, no forma parte de los lotes evaluados):

```text
- País: Kenia
- Región: Nyeri
- Variedad: SL28 y SL34
- Proceso: Lavado
- Altitud: 1.800 msnm
- Notas de cata: grosella negra, pomelo, panela
- Acidez y cuerpo: acidez intensa y jugosa, cuerpo medio
- Puntaje SCA: 88
- Tueste: claro
- Método sugerido: V60
```

```json
{
  "nombre_comercial": "Kenia Nyeri · Grosella y Pomelo",
  "descripcion_menu": "Un café vibrante y jugoso: recuerda a un jugo de grosellas con un toque de pomelo y un final dulce de panela. Ideal para quienes buscan una taza fresca y llena de fruta.",
  "descripcion_extendida": "Cultivado a 1.800 metros en las laderas de Nyeri, este lote de variedades SL28 y SL34 pasó por un proceso lavado que deja su sabor muy limpio y definido. En la taza encontrarás una acidez intensa, parecida a la de una fruta recién cortada, que recuerda a la grosella negra y al pomelo, y que termina con un dulzor de panela. Con 88 puntos en la escala SCA, es un café para descubrir sin azúcar y apreciar cómo cambia a medida que se enfría.",
  "notas_para_cliente": [
    {
      "nota": "grosella negra",
      "analogia": "como un jugo de berries poco dulce"
    },
    {
      "nota": "pomelo",
      "analogia": "frescura cítrica, similar a la cáscara del pomelo"
    },
    {
      "nota": "panela",
      "analogia": "dulzor parecido a la chancaca"
    }
  ],
  "intensidad_1a5": 3,
  "acidez_1a5": 5,
  "preparacion_recomendada": "En V60, 15 g de café para 250 ml de agua a 93 °C; tiempo total cercano a 3 minutos.",
  "maridaje": "Kuchen de frambuesa",
  "ideal_para": "Quien disfruta los sabores frutales y quiere salir del café amargo tradicional."
}
```

## P2 — Copy para Instagram

```text
### CONTEXTO ###
La cafetería lanzará este café esta semana. Esta es su ficha sensorial aprobada:
"""
{ficha_cliente}
"""

### TAREA ###
Escribe 3 publicaciones distintas para el feed de Instagram, una por enfoque: "origen", "sensorial" y "educativo".

### FORMATO DE SALIDA ###
JSON con la clave "variantes": lista de 3 objetos con claves
enfoque, gancho (primera línea, máx. 10 palabras), cuerpo (50 a 110 palabras), llamado_a_la_accion (una oración), hashtags (lista de 5 a 8, sin espacios).

### RESTRICCIONES ###
- Máximo 3 emojis por publicación.
- Menciona el precio solo en una de las tres variantes: {precio}.
- No uses información que no esté en la ficha sensorial.
- Hashtags en español y en formato CamelCase, sin tildes ni duplicados (ej.: #CafeDeEspecialidad), salvo #specialtycoffee.
- Reescribe con palabras propias: no copies oraciones textuales de la ficha sensorial.
- Cada variante debe abrir con un gancho distinto (pregunta, dato de origen o invitación).
```

## P3 — Meta-prompt: construcción del prompt de imagen

```text
### CONTEXTO ###
Necesitamos una fotografía publicitaria de producto para Instagram (formato vertical 4:5) de este café:
"""
{ficha_tecnica}
"""

### TAREA (sigue los pasos en orden) ###
Paso 1 — En "analisis_visual", asocia cada nota de cata a un elemento visual concreto (fruta, flor, ingrediente, color, textura)
y el origen/proceso a una ambientación sutil (materiales, paisaje, luz). Justifica cada asociación en pocas palabras.
Paso 2 — En "prompt_imagen", redacta en INGLÉS un prompt de 55 a 75 palabras (frases cortas separadas por comas) con este orden:
[sujeto principal: taza y bolsa de café sin etiqueta] + [elementos de las notas] + [ambientación del origen]
+ [composición y encuadre] + [iluminación] + [estilo fotográfico y lente] + [paleta de colores] + [calidad].
Paso 3 — En "prompt_negativo", lista en inglés lo que se debe evitar.

### FORMATO DE SALIDA ###
JSON con claves: analisis_visual (lista de objetos {{"elemento_ficha", "representacion_visual", "motivo"}}),
prompt_imagen (string), prompt_negativo (string), relacion_aspecto ("4:5").

### RESTRICCIONES ###
- La imagen NO debe contener texto, letras, logos ni marcas (los modelos de imagen los deforman; la marca se agrega después).
  Estas exclusiones van SOLO en "prompt_negativo": no escribas "text", "logo" ni "label" en el prompt positivo,
  porque mencionarlos puede inducir al modelo a dibujarlos. Describe la bolsa como "plain unbranded".
- Estética natural y realista, no caricaturesca. Sin personas.
- No incluyas elementos que contradigan las notas de cata.
```

## P4 — Guion para locución (texto → audio)

```text
### CONTEXTO ###
Ficha sensorial del café:
"""
{ficha_cliente}
"""

### TAREA ###
Escribe el guion de una locución de unos 30 segundos (5 o 6 oraciones, entre 60 y 80 palabras) para un Reel de Instagram
y para la versión accesible de la carta (personas con discapacidad visual).

### RESTRICCIONES ###
- Texto plano, sin emojis, hashtags, viñetas, comillas ni símbolos.
- Frases cortas y naturales para ser leídas por una voz sintética.
- Escribe los números en palabras (por ejemplo, "mil novecientos metros").
- Termina invitando a probarlo en {marca}.
```

## P5 — Rúbrica de evaluación (LLM-as-judge)

```text
### ROL ###
Actúa como evaluador imparcial de contenidos de marketing de café de especialidad.

### FICHA TÉCNICA (fuente de verdad) ###
"""
{ficha_tecnica}
"""

### TEXTO A ###
"""
{texto_a}
"""

### TEXTO B ###
"""
{texto_b}
"""

### CONTEXTO DE USO ###
Cada texto debe servir como descripción del café en la web y en la carta de la cafetería y se publicará TAL CUAL:
no puede requerir que el dueño elija entre opciones, borre instrucciones, quite formato markdown o recorte extensión
(máximo recomendado: 120 palabras).

### TAREA ###
Evalúa cada texto de 1 (muy deficiente) a 5 (excelente) en:
fidelidad (no inventa ni omite datos clave de la ficha; cualquier método, sabor o dato ausente en la ficha es un dato inventado),
claridad (lo entiende alguien sin formación en cata), tono_marca (cercano, experto, español de Chile neutro, sin voseo),
persuasion (motiva la compra), uso_directo (se puede publicar tal cual según el contexto de uso).
Lista además los datos inventados que detectes en cada texto. Sé estricto y justifica con evidencia.

### FORMATO DE SALIDA ###
JSON: {{"A": {{"fidelidad": int, "claridad": int, "tono_marca": int, "persuasion": int, "uso_directo": int, "datos_inventados": [str]}},
        "B": {{...mismas claves...}}, "comentario": "máx. 40 palabras"}}
```

---

## Ficha técnica de ejemplo (formato de entrada)

```text
- País: Etiopía
- Región: Guji, Oromía
- Productor: Estación de lavado Hambela (cooperativa de pequeños productores)
- Variedad: Heirloom etíope (variedades locales)
- Proceso: Natural (secado en camas africanas por 21 días)
- Altitud: 1.950–2.100 msnm
- Notas de cata: arándano, frutilla madura, chocolate de leche, jazmín
- Acidez y cuerpo: acidez brillante tipo frutos rojos, cuerpo medio y sedoso
- Puntaje SCA: 87
- Tueste: claro
- Método sugerido: V60 o Chemex
- Precio: $12.900 (250 g)
```
