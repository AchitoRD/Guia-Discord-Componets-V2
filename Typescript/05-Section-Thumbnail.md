# 05 - Section y Thumbnail

`SectionBuilder` coloca texto **lado a lado** con un accesorio - `ThumbnailBuilder` (imagen) o `ButtonBuilder`.

REQUIERE accesorio. Sin `setThumbnailAccessory()` o `setButtonAccessory()` lanza error. Si no tienes imagen/boton, usa `TextDisplayBuilder` directo.

## Con miniatura (imagen)

```typescript
import { SectionBuilder, ThumbnailBuilder, TextDisplayBuilder, MessageFlags } from 'discord.js';

const section = new SectionBuilder()
    .addTextDisplayComponents(
        new TextDisplayBuilder().setContent("**Juan Perez**"),
        new TextDisplayBuilder().setContent("Ticket de soporte abierto."),
    )
    .setThumbnailAccessory(
        new ThumbnailBuilder().setURL("https://example.com/avatar.png"),
    );

await channel.send({
    components: [section],
    flags: MessageFlags.IsComponentsV2,
});
```

## Avatar de usuario

```typescript
const section = new SectionBuilder()
    .addTextDisplayComponents(
        new TextDisplayBuilder().setContent(`**${interaction.user.displayName}**`),
        new TextDisplayBuilder().setContent("Categoria: General"),
    )
    .setThumbnailAccessory(
        new ThumbnailBuilder()
            .setURL(interaction.user.displayAvatarURL())
            .setDescription("Avatar"),
    );
```

## Icono del servidor

```typescript
const iconUrl = interaction.guild.iconURL();

const section = new SectionBuilder()
    .addTextDisplayComponents(
        new TextDisplayBuilder().setContent(`**__${title}__**\n\n${description}`),
    )
    .setThumbnailAccessory(
        new ThumbnailBuilder().setURL(iconUrl),
    );
```

## Boton como accesorio

```typescript
import { SectionBuilder, TextDisplayBuilder, ButtonBuilder, ButtonStyle } from 'discord.js';

const section = new SectionBuilder()
    .addTextDisplayComponents(
        new TextDisplayBuilder().setContent("Count: 0").setId(1001),
    )
    .setButtonAccessory(
        new ButtonBuilder()
            .setLabel("+1")
            .setStyle(ButtonStyle.Primary)
            .setCustomId("increment_btn"),
    );
```

## Thumbnail spoiler

```typescript
new ThumbnailBuilder()
    .setURL("https://example.com/image.png")
    .setSpoiler(true);
```

## Notas

- Una seccion acepta **1 a 3** `TextDisplayBuilder`
- El accesorio es **OBLIGATORIO**
- `ThumbnailBuilder` solo acepta URL - usa `attachment://filename` para archivos locales
- La seccion puede estar en primer nivel o dentro de un `ContainerBuilder`
