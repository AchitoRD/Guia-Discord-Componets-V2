# 03 - TextDisplay

`TextDisplayBuilder` renderiza texto Markdown dentro de un layout CV2.

## Uso basico

```typescript
import { TextDisplayBuilder, MessageFlags } from 'discord.js';

const text = new TextDisplayBuilder().setContent("Hola, mundo!");

await channel.send({
    components: [text],
    flags: MessageFlags.IsComponentsV2,
});
```

## Markdown soportado

```typescript
new TextDisplayBuilder().setContent("### Ticket Abierto");
new TextDisplayBuilder().setContent("**Estado:** Abierto");
new TextDisplayBuilder().setContent(">>> Blockquote");
new TextDisplayBuilder().setContent("`codigo inline`");
new TextDisplayBuilder().setContent("-# Texto pequeno");
```

## Texto multilinea

```typescript
new TextDisplayBuilder().setContent(
    "**Usuario:** Juan\n" +
    "**Categoria:** Soporte\n" +
    "**Creado:** <t:1700000000:R>",
);
```

## Timestamps

```typescript
const ts = Math.floor(Date.now() / 1000);

new TextDisplayBuilder().setContent(`Abierto <t:${ts}:R>`);
new TextDisplayBuilder().setContent(`Abierto <t:${ts}:F>`);
```

## Menciones - usan allowedMentions

```typescript
await channel.send({
    components: [
        new TextDisplayBuilder().setContent(`Creado por <@${user.id}>`),
    ],
    flags: MessageFlags.IsComponentsV2,
    allowedMentions: { users: [] },
});
```

## Component ID

```typescript
new TextDisplayBuilder()
    .setContent("Count: 0")
    .setId(1001);
```

## Notas

- Sin limite de cantidad de `TextDisplayBuilder`
- No hay separacion titulo/descripcion como en embeds - usa `#`, `##`, `###`
- Maximo **4000 caracteres** en total por mensaje
