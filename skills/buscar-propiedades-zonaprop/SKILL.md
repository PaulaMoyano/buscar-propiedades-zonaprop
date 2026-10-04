---
name: buscar-propiedades-zonaprop
description: Busca propiedades en venta en Zonaprop (Argentina) con el MCP de Playwright y cuenta los mejores resultados. Usá esta skill siempre que Paula quiera comprar una casa, departamento o PH, pida buscar propiedades en Zonaprop, "qué hay en venta en <barrio>", o dé un presupuesto y un barrio para comprar, aunque no nombre Zonaprop.
---

# buscar-propiedades-zonaprop

Abre zonaprop.com.ar con el navegador de Playwright, hace la búsqueda que pida Paula y le cuenta los 5 mejores resultados.

## Requisito

Necesita las herramientas del MCP de Playwright (`mcp__playwright__browser_*`). Si no están disponibles en la sesión, avisale a Paula que falta conectar el MCP (`claude mcp add playwright -- npx @playwright/mcp@latest`) y que hay que reiniciar la sesión. No improvises otra forma de scrapear.

## Qué pedirle a Paula

Siempre compra. Antes de buscar necesitás tres datos; preguntá solo los que falten:
1. **Tipo:** casa, departamento o PH.
2. **Barrio** (uno o varios).
3. **Presupuesto máximo** y su moneda (en venta suele ser USD).

Si ya te los dio en el mensaje, buscá directo sin repreguntar. Ambientes, metros mínimos o cochera son opcionales: aplicalos solo si los menciona.

## Cómo buscar

1. Navegá directo a la URL de resultados: `https://www.zonaprop.com.ar/<tipo>-venta-<barrio>-menos-<precio>-dolar.html` (por ejemplo `casas-venta-belgrano-menos-250000-dolar.html`; tipos: `casas`, `departamentos`, `ph`). Verificá en el título de la página que el filtro se aplicó ("22 Casas hasta USD 250.000 en venta en Belgrano"). Si no, usá el buscador y los filtros del sitio.
2. Extraé las tarjetas con `browser_evaluate` sobre `[data-posting-type]` (más liviano que el snapshot): `innerText` de cada una y el `data-to-posting` o el primer `href` para el link. De cada una sacá: precio, expensas, m² totales, ambientes, dirección y link.
3. **Filtrá el ruido.** La página mezcla avisos que no cumplen: mansiones con "Consultar precio" (se cuelan igual en los filtros de precio), avisos "Reservado", emprendimientos "En Pozo" y, al final, propiedades de barrios vecinos. Quedate solo con los que tienen precio visible dentro del presupuesto, están en el barrio pedido y son del tipo pedido. Decile a Paula cuántos de los resultados reales eran válidos.
4. Un precio que no cuadra (por ejemplo "$ 880.000" para una casa de 311 m²) no lo uses ni lo corrijas: abrí la ficha para confirmarlo y, si sigue raro, marcalo con ❓ para que Paula consulte al anunciante.
5. Si hace falta saber más de una candidata fuerte, abrí su ficha.
6. Cerrá el navegador al terminar.

## Si el sitio bloquea

Zonaprop puede mostrar un captcha o una verificación anti-bots. Si pasa, **frená y avisale a Paula**; no intentes eludir la protección ni insistir en bucle. Ofrecele que haga la verificación a mano en la ventana del navegador y retomás, o que te pase el link de una búsqueda ya filtrada.

## Qué es un "mejor resultado"

Ordená por la mejor relación **precio por m²** dentro de los filtros de Paula, y descartá avisos sin precio o sin metros claros. Si dos están parejos, preferí el que tenga menos expensas y más ambientes. Aclarale que es un criterio de precio y que no evalúa estado ni ubicación exacta.

## Formato de la respuesta

Español rioplatense, markdown prolijo y emojis. Empezá con una línea del tipo `🏠 Casas en venta · Palermo · hasta USD 300.000 · 24 avisos revisados`, y después una tabla con los 5 mejores:

| # | Propiedad | Precio | m² | USD/m² | Amb. | Expensas | Link |
|---|---|---|---|---|---|---|---|

Debajo, 1–2 líneas por cada una de las 3 primeras con qué la destaca (🏆 mejor precio/m², 🛏️ más ambientes, etc.). Si hay pocos resultados válidos, ofrecé alternativas en una tabla aparte (PH o departamento, otro barrio) y aclaralo. Cerrá con `> 💡` indicando si conviene ajustar la búsqueda (subir el presupuesto, sumar barrios). Los datos son los del aviso en el momento de la consulta; no los embellezcas ni inventes lo que no figure.
