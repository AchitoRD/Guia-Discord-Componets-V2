# 3. TextDisplay

`TextDisplayBuilder` renderiza texto Markdown dentro de un diseño CV2. Es la forma principal de mostrar cualquier contenido escrito — títulos, descripciones, información tipo campo, etc. Reemplaza directamente el antiguo campo `content` y las descripciones de embeds.

---

## Uso Básico

```js
const { TextDisplayBuilder, MessageFlags } = require('discord.js');

const text = new TextDisplayBuilder()
  .setContent('¡Hola, mundo!');

await channel.send({
  components: [text],
  flags: MessageFlags.IsComponentsV2,
});
```

## Markdown Totalmente Soportado

```js
new TextDisplayBuilder().setContent('### Ticket Abierto')
new TextDisplayBuilder().setContent('**Estado:** Abierto')
new TextDisplayBuilder().setContent('>>> Esto es un blockquote')
new TextDisplayBuilder().setContent('`código inline` también funciona')
new TextDisplayBuilder().setContent('-# Texto pequeño al final')
```

## Texto Multilínea

```js
new TextDisplayBuilder().setContent(
  '**Usuario:** Juan\n' +
  '**Categoría:** Soporte\n' +
  '**Creado:** <t:1700000000:R>'
)
```

## Marcas de Tiempo de Discord

```js
const ts = Math.floor(Date.now() / 1000);

new TextDisplayBuilder().setContent(`Abierto <t:${ts}:R>`)  // "hace 5 minutos"
new TextDisplayBuilder().setContent(`Abierto <t:${ts}:F>`)  // "lunes, 1 de enero de 2024 12:00"
```

## Menciones de Usuario y Canal

Las menciones dentro de `TextDisplayBuilder` **notificarán a usuarios y roles** — usa `allowedMentions` para controlar esto:

```js
await channel.send({
  components: [
    new TextDisplayBuilder().setContent(`Creado por <@${user.id}>`)
  ],
  flags: MessageFlags.IsComponentsV2,
  allowedMentions: { users: [] }, // suprimir el ping
});
```

## Establecer un ID de Componente

```js
const DISPLAY_ID = 1001;

new TextDisplayBuilder()
  .setContent('Count: 0')
  .setId(DISPLAY_ID)
```

Usa `.setId()` para poder encontrar y actualizar este componente después mediante respuestas de interacción.

## Notas

- Puedes tener tantos `TextDisplayBuilder` como necesites
- No hay división título/descripción como en los embeds — usa encabezados Markdown (`#`, `##`, `###`)
- El texto total en **todos** los componentes `TextDisplayBuilder` en un mensaje no puede exceder **4000 caracteres**
- Los emojis (unicode y personalizados de Discord) se renderizan bien
