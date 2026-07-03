# 5. Section y Thumbnail

`SectionBuilder` coloca texto **lado a lado** con un accesorio — ya sea un `ThumbnailBuilder` (imagen) o un `ButtonBuilder`. Útil para tarjetas de perfil, paneles de información de usuario, o cualquier combinación de texto + imagen.

---

## Con una Thumbnail (Imagen)

```js
const { SectionBuilder, ThumbnailBuilder, TextDisplayBuilder, MessageFlags } = require('discord.js');

const section = new SectionBuilder()
  .addTextDisplayComponents(
    new TextDisplayBuilder().setContent('**Juan Pérez**'),
    new TextDisplayBuilder().setContent('Ticket de soporte abierto.')
  )
  .setThumbnailAccessory(
    new ThumbnailBuilder().setURL('https://example.com/avatar.png')
  );

await channel.send({
  components: [section],
  flags: MessageFlags.IsComponentsV2,
});
```

## Con el Avatar de un Usuario

```js
const section = new SectionBuilder()
  .addTextDisplayComponents(
    new TextDisplayBuilder().setContent(`**${interaction.user.displayName}**`),
    new TextDisplayBuilder().setContent('Categoría: General')
  )
  .setThumbnailAccessory(
    new ThumbnailBuilder()
      .setURL(interaction.user.displayAvatarURL())
      .setDescription('Avatar de usuario')
  );
```

## Con el Icono del Servidor

```js
const iconUrl = interaction.guild.iconURL();

const section = new SectionBuilder()
  .addTextDisplayComponents(
    new TextDisplayBuilder().setContent(`**__${title}__**\n\n${description}`)
  )
  .setThumbnailAccessory(
    new ThumbnailBuilder().setURL(iconUrl)
  );
```

## Cómo se Renderiza

```
┌─────────────────────────────────────────┐
│  Contenido de texto aquí          [img] │
│  Más texto en el lado izquierdo         │
└─────────────────────────────────────────┘
```

La miniatura aparece a la **derecha**, el texto a la **izquierda**.

## Botón como Accesorio

Un `ButtonBuilder` también puede ser el accesorio de la sección — colocado en el lado derecho junto al texto:

```js
const { SectionBuilder, TextDisplayBuilder, ButtonBuilder, ButtonStyle } = require('discord.js');

const section = new SectionBuilder()
  .addTextDisplayComponents(
    new TextDisplayBuilder().setContent('Count: 0').setId(1001)
  )
  .setButtonAccessory(
    new ButtonBuilder()
      .setLabel('+1')
      .setStyle(ButtonStyle.Primary)
      .setCustomId('increment_btn')
  );
```

Útil para combos compactos de acción + etiqueta sin necesidad de un `ActionRowBuilder` completo.

## Thumbnail Spoiler

```js
new ThumbnailBuilder()
  .setURL('https://example.com/image.png')
  .setSpoiler(true)
```

## Notas

- Una sección puede contener de **1 a 3** elementos `TextDisplayBuilder`
- El accesorio puede ser un `ThumbnailBuilder` **o** un `ButtonBuilder` — ambos están soportados
- `ThumbnailBuilder` solo acepta una URL — usa `attachment://filename` para archivos locales
- La sección puede estar en el primer nivel o dentro de un `ContainerBuilder`
