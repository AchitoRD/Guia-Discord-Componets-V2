# 01 - Message Structure

Requisito: discord.js >= 14.23.0

Un mensaje CV2 usa `components` (array de builders) + `MessageFlags.IsComponentsV2`.

## Estructura basica

```typescript
import { MessageFlags } from 'discord.js';

await channel.send({
    components: [/* tus componentes aqui */],
    flags: MessageFlags.IsComponentsV2,
});
```

## Responder a interaccion

```typescript
await interaction.reply({
    components: [container],
    flags: MessageFlags.IsComponentsV2,
});
```

## Respuesta efimera

```typescript
await interaction.reply({
    components: [container],
    flags: MessageFlags.IsComponentsV2 | MessageFlags.Ephemeral,
});
```

## Despues de un defer

```typescript
await interaction.deferReply();

// ... trabajo async ...

await interaction.editReply({
    components: [container],
    flags: MessageFlags.IsComponentsV2,
});
```

No necesitas la bandera al hacer defer - solo al enviar el contenido.

## Multiples componentes de primer nivel

```typescript
await channel.send({
    components: [textDisplay, separator, container],
    flags: MessageFlags.IsComponentsV2,
});
```

## Component id vs custom_id

Cada componente puede tener un `id` numerico opcional - NO es lo mismo que `custom_id`.

```typescript
new TextDisplayBuilder().setContent("Count: 0").setId(1001);
```

| Campo | Tipo | Proposito |
|-------|------|-----------|
| `id` | number | Encontrar componente en respuestas |
| `custom_id` | string | Identificador en botones/selects |

## Notas

- La bandera `IsComponentsV2` **no puede eliminarse** del mensaje una vez enviado
- Con CV2 activo, `content`, `embeds`, `stickers`, `polls` estan bloqueados
- Los mensajes antiguos con `components: [actionRow]` siguen funcionando - CV2 es optativo
- Limite: **40 componentes** por mensaje (incluyendo anidados)
- Limite texto: **4000 caracteres** en todo `TextDisplayBuilder`
