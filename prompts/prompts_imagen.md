# Prompts texto-imagen — CafePrompt

## Herramienta y configuración

Las imágenes se generaron **manualmente** (sin API, como indica la consigna al no usar DALL·E) en **Google AI Studio → Playground**, con el modelo gratuito **Nano Banana 2 Lite** (`gemini-3.1-flash-lite-image`). Se intentó primero con NightCafe, pero en modo invitado solo permite una imagen y exige crear una cuenta.

| Parámetro | Valor |
|---|---|
| Modelo | Nano Banana 2 Lite (`gemini-3.1-flash-lite-image`) |
| Formato de salida | Images only |
| Relación de aspecto | 4:5 → 928 × 1152 px |
| Resolución | 1K |
| Temperatura | 1 |
| Contexto | chat nuevo por imagen |
| Negative prompt | no disponible en la interfaz; se documenta el generado por P3 y las exclusiones se aplican omitiendo texto/logos del prompt positivo |
| Fecha | 23-09-2026 |

Los prompts v2 **no fueron escritos a mano**: los generó el modelo de texto con el meta-prompt P3 (ver [prompts_texto.md](prompts_texto.md)) y se copiaron tal cual en la herramienta.

---

## 1. Etiopía — v1 · prompt básico (línea base)

```text
a cup of coffee from Ethiopia
```

![Etiopía v1](../images/01_etiopia_v1_prompt_basico.jpg)

**Resultado:** escena genérica de ceremonia tradicional con una persona; no muestra el producto (bolsa), no representa las notas de cata y no sirve como foto de producto para la tienda.

---

## 2. Etiopía — v2 · prompt optimizado (generado por P3)

**Razonamiento visual del modelo (campo `analisis_visual`):**

| Elemento de la ficha | Representación visual | Motivo |
|---|---|---|
| arándano y frutilla madura | frutas frescas enteras y dispuestas al lado de la taza | comunican visualmente la acidez brillante a frutos rojos y la jugosidad descrita en la cata |
| chocolate de leche | trozos sutiles de chocolate suave en el fondo | refuerzan la sensación de dulzura cremosa y cuerpo sedoso del café |
| jazmín | delicadas flores blancas frescas cerca del plato | evocan el aroma floral sutil presente en la taza |
| Etiopía Guji y proceso natural | superficie de madera rústica clara y luz solar suave de la mañana | transmiten la calidez y el origen natural del café sin saturar la escena |

**Prompt:**

```text
Vertical 4:5 photograph, a ceramic cup of black drip coffee, a plain unbranded paper coffee bag, fresh blueberries, ripe red strawberries, delicate white jasmine flowers, milk chocolate pieces, rustic light wooden table, soft morning sunlight, top-down angle, professional food photography, macro details, shot on 85mm lens, f/2.8, warm natural color palette, highly detailed, photorealistic
```

**Negative prompt (documentado):**

```text
text, logo, label, brand, letters, watermark, blurry, cartoon, illustration, painting, deformed, ugly, extra cups, people, hands, dark shadows
```

![Etiopía v2](../images/02_etiopia_v2_prompt_optimizado.jpg)

**Resultado:** Taza de filtrado, bolsa kraft sin marca, arándanos, frutillas, chocolate de leche y jazmín: las 4 notas de cata quedan representadas, sin texto ni personas.

---

## 3. Colombia — v2 · prompt optimizado (generado por P3)

**Razonamiento visual del modelo (campo `analisis_visual`):**

| Elemento de la ficha | Representación visual | Motivo |
|---|---|---|
| Mandarina | Gajos de mandarina fresca y brillante junto a la taza | Evoca directamente la nota de acidez cítrica jugosa sin necesidad de texto. |
| Panela | Un trozo rústico de panela en bloque sobre una tabla de madera | Representa visualmente el dulzor característico de este café. |
| Flor de azahar | Flores blancas sutiles y delicadas dispuestas al lado | Conecta con la nota floral de la cata de manera elegante y natural. |
| Té negro | Taza de cerámica artesanal con café filtrado limpio y traslúcido | Muestra el cuerpo ligero y limpio propio de este perfil en taza. |
| Origen Colombia, Pitalito, Huila | Superficie de madera cálida y hojas verdes tropicales difuminadas de fondo | Sugiere el origen cafetalero de alta montaña de forma sutil y acogedora. |

**Prompt:**

```text
Vertical 4:5 photograph, ceramic dripper cup with clean black coffee, plain unbranded craft paper coffee bag, fresh mandarin orange segments, rustic block of panela, delicate white orange blossom flowers, warm wooden tabletop background, soft natural morning sunlight filtering through tropical leaves, flat lay composition, overhead angle, professional food photography, 85mm lens, f/2.8, shallow depth of field, natural earthy color palette, crisp focus, photorealistic
```

**Negative prompt (documentado):**

```text
text, words, letters, brand, logo, label, watermark, blurry, painting, illustration, cartoon, 3d render, plastic, artificial lighting, people, hands, milk, sugar cubes
```

![Colombia v2](../images/03_colombia_v2_prompt_optimizado.jpg)

**Resultado:** Mandarina, panela, flor de azahar y luz de mañana entre hojas tropicales; bolsa sin marca y sin texto. Composición limpia y coherente con el perfil cítrico-floral.

---

## 4. Brasil — v2 · prompt optimizado (generado por P3)

**Razonamiento visual del modelo (campo `analisis_visual`):**

| Elemento de la ficha | Representación visual | Motivo |
|---|---|---|
| avellana tostada | Avellanas enteras y tostadas dispuestas de forma natural junto a la taza. | Evoca directamente la nota avellanada y aporta una textura terrosa. |
| cacao | Trozo de chocolate oscuro artesanal semi-partido. | Representa visualmente la nota de cacao con un tono marrón profundo. |
| caramelo | Gotas doradas de caramelo líquido suave. | Refuerza la sensación de dulzor y cuerpo cremoso. |
| ciruela seca | Ciruelas pasas con textura arrugada y carnosa. | Muestra la nota frutal madura y sutilmente dulce. |
| proceso pulped natural y origen Brasil | Superficie de madera cálida y rústica con granos de café crudos dispersos. | Sugiere un origen terroso y un proceso artesanal cuidado. |

**Prompt:**

```text
Vertical 4:5, Ceramic ceramic cup filled with creamy espresso, plain unbranded craft coffee bag, roasted hazelnuts, dark chocolate pieces, soft golden caramel drops, dried plums, rustic wooden tabletop background, warm cozy morning sunlight filtering through, close-up macro photography, shallow depth of field, 85mm lens, f/2.8, rich brown and amber color palette, photorealistic, highly detailed, professional food photography
```

**Negative prompt (documentado):**

```text
text, letters, words, logo, brand, labels, watermark, deformed cup, cartoon, illustration, drawing, blender, mug with handle facing away, bright neon lights, blurry, low resolution, extra fingers, people, hands
```

![Brasil v2](../images/04_brasil_v2_prompt_optimizado.jpg)

**Resultado:** Avellanas, chocolate, caramelo y ciruelas secas con paleta cálida. Falla: el modelo escribió «BRAZIL – Cerrado Natural» en la bolsa pese a «plain unbranded»; la taza parece más un latte que un espresso.

---

## Conclusión del experimento

El prompt básico produce una imagen genérica y no utilizable comercialmente. El prompt optimizado (estructura: sujeto → elementos de las notas → ambientación → composición → luz → estilo/lente → paleta → calidad) produce fotos de producto coherentes con el perfil sensorial de cada café. La principal limitación observada es el texto no solicitado en el envase (Brasil), que requeriría un generador con *negative prompt* real o una etapa de edición.
