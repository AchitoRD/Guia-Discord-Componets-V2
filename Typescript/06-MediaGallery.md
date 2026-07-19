# 06 - MediaGallery

`MediaGalleryBuilder` muestra imagenes/videos en cuadricula.

## Imagen unica

```typescript
import { MediaGalleryBuilder, MediaGalleryItemBuilder, MessageFlags } from 'discord.js';

const gallery = new MediaGalleryBuilder()
    .addItems(
        new MediaGalleryItemBuilder().setURL("https://example.com/banner.png"),
    );

await channel.send({
    components: [gallery],
    flags: MessageFlags.IsComponentsV2,
});
```

## Multiples imagenes

```typescript
const gallery = new MediaGalleryBuilder()
    .addItems(
        new MediaGalleryItemBuilder().setURL("https://example.com/1.png").setDescription("Img 1"),
        new MediaGalleryItemBuilder().setURL("https://example.com/2.png").setDescription("Img 2"),
        new MediaGalleryItemBuilder().setURL("https://example.com/3.png").setDescription("Img 3"),
    );
```

## Con alt text y spoiler

```typescript
new MediaGalleryItemBuilder()
    .setURL("https://example.com/image.png")
    .setDescription("Screenshot del dashboard")
    .setSpoiler(true);
```

## Archivo local

```typescript
import { AttachmentBuilder } from 'discord.js';

const file = new AttachmentBuilder("./assets/banner.png").setName("banner.png");

const gallery = new MediaGalleryBuilder()
    .addItems(
        new MediaGalleryItemBuilder().setURL("attachment://banner.png"),
    );

await channel.send({
    components: [gallery],
    files: [file],
    flags: MessageFlags.IsComponentsV2,
});
```

## Dentro de un container

```typescript
const container = new ContainerBuilder()
    .addTextDisplayComponents(
        new TextDisplayBuilder().setContent("### Screenshots"),
    )
    .addSeparatorComponents(
        new SeparatorBuilder().setDivider(true).setSpacing(SeparatorSpacingSize.Small),
    )
    .addMediaGalleryComponents(
        new MediaGalleryBuilder().addItems(
            new MediaGalleryItemBuilder().setURL(imageUrl),
        ),
    );
```

## Notas

- Hasta **10 items** por galeria
- Soporta imagenes, videos y GIFs via URL
- Usa `attachment://filename` para archivos locales
