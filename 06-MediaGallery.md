# 6. MediaGallery

`MediaGalleryBuilder` muestra una o más imágenes o videos en una cuadrícula. Es el reemplazo de CV2 para `embed.setImage()` y las vistas previas de archivos adjuntos.

---

## Imagen Única

```js
const { MediaGalleryBuilder, MediaGalleryItemBuilder, MessageFlags } = require('discord.js');

const gallery = new MediaGalleryBuilder()
  .addItems(
    new MediaGalleryItemBuilder().setURL('https://example.com/banner.png')
  );

await channel.send({
  components: [gallery],
  flags: MessageFlags.IsComponentsV2,
});
```

## Múltiples Imágenes (Cuadrícula)

```js
const gallery = new MediaGalleryBuilder()
  .addItems(
    new MediaGalleryItemBuilder().setURL('https://example.com/image1.png').setDescription('Imagen 1'),
    new MediaGalleryItemBuilder().setURL('https://example.com/image2.png').setDescription('Imagen 2'),
    new MediaGalleryItemBuilder().setURL('https://example.com/image3.png').setDescription('Imagen 3')
  );
```

## Con Texto Alternativo y Spoiler

```js
new MediaGalleryItemBuilder()
  .setURL('https://example.com/image.png')
  .setDescription('Una captura de pantalla del panel')
  .setSpoiler(true)
```

## Usando un Archivo Local

```js
const { AttachmentBuilder } = require('discord.js');

const file = new AttachmentBuilder('./assets/banner.png').setName('banner.png');

const gallery = new MediaGalleryBuilder()
  .addItems(
    new MediaGalleryItemBuilder().setURL('attachment://banner.png')
  );

await channel.send({
  components: [gallery],
  files: [file],
  flags: MessageFlags.IsComponentsV2,
});
```

## Ejemplo con Avatar de Usuario

```js
const gallery = new MediaGalleryBuilder()
  .addItems(
    new MediaGalleryItemBuilder()
      .setURL(interaction.user.displayAvatarURL({ size: 512 }))
      .setDescription(`Avatar de ${interaction.user.username}`)
  );
```

## Dentro de un Container

```js
const container = new ContainerBuilder()
  .addTextDisplayComponents(
    new TextDisplayBuilder().setContent('### Capturas de Pantalla')
  )
  .addSeparatorComponents(
    new SeparatorBuilder().setDivider(true).setSpacing(SeparatorSpacingSize.Small)
  )
  .addMediaGalleryComponents(
    new MediaGalleryBuilder().addItems(
      new MediaGalleryItemBuilder().setURL(imageUrl)
    )
  );
```

## Notas

- Hasta **10 elementos** por galería
- Soporta imágenes, videos y GIFs mediante URL
- Usa `attachment://filename` para archivos locales junto con `files: [attachment]`
