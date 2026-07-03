# 1. Estructura del Mensaje

En Discord.js no existe una clase `LayoutView`. Un mensaje CV2 es simplemente una llamada normal a `send()` o `reply()` con dos cosas clave:

1. Un array `components` que contiene tus builders
2. La bandera `MessageFlags.IsComponentsV2`

---

## Estructura Básica

```js
const { MessageFlags } = require('discord.js');

await channel.send({
  components: [/* tus componentes aquí */],
  flags: MessageFlags.IsComponentsV2,
});
```

## Responder a una Interacción

```js
await interaction.reply({
  components: [container],
  flags: MessageFlags.IsComponentsV2,
});
```

## Respuesta Efímera

```js
await interaction.reply({
  components: [container],
  flags: MessageFlags.IsComponentsV2 | MessageFlags.Ephemeral,
});
```

## Seguimiento Después de un Defer

```js
await interaction.deferReply();

// ... trabajo asíncrono ...

await interaction.editReply({
  components: [container],
  flags: MessageFlags.IsComponentsV2,
});
```

> No necesitas la bandera al hacer defer — solo cuando envías el contenido real del componente.

## Múltiples Componentes de Primer Nivel

Puedes pasar múltiples builders en el array — se apilan verticalmente:

```js
await channel.send({
  components: [textDisplay, separator, container],
  flags: MessageFlags.IsComponentsV2,
});
```

## Cómo Encaja Todo

```
components: [
  ContainerBuilder
  ├── TextDisplayBuilder
  ├── SeparatorBuilder
  ├── SectionBuilder
  │   ├── TextDisplayBuilder
  │   └── ThumbnailBuilder (accesorio)
  ├── MediaGalleryBuilder
  └── ActionRowBuilder
      └── ButtonBuilder
]
```

## `id` del Componente vs `custom_id`

Cada componente puede tener un campo `id` numérico opcional — **no** es lo mismo que `custom_id`.

```js
new TextDisplayBuilder().setContent('Count: 0').setId(1001)
```

- `id` — un número que estableces para encontrar un componente después en respuestas de interacción
- `custom_id` — un string en botones/selects que activa eventos de interacción

## Notas

- La bandera `IsComponentsV2` **no puede eliminarse** de un mensaje una vez enviado
- Una vez establecida, `content`, `embeds`, `stickers` y `polls` están bloqueados en ese mensaje
- Los mensajes con `components: [actionRow]` al estilo antiguo siguen funcionando — CV2 es optativo por mensaje
- Límite total de componentes: **40** (incluyendo anidados)
- Límite total de texto: **4000 caracteres** en todo el contenido de `TextDisplayBuilder`
