# 02 - Container

`ContainerBuilder` es el bloque principal de un layout CV2. Agrupa componentes con barra de acento opcional.

## Uso basico

```typescript
import { ContainerBuilder, TextDisplayBuilder, MessageFlags } from 'discord.js';

const container = new ContainerBuilder()
    .addTextDisplayComponents(
        new TextDisplayBuilder().setContent("Hola!"),
    );

await channel.send({
    components: [container],
    flags: MessageFlags.IsComponentsV2,
});
```

## Con color de acento

```typescript
const container = new ContainerBuilder()
    .setAccentColor(0x5865F2); // Discord blurple
```

Colores:

```typescript
new ContainerBuilder().setAccentColor(0x57F287); // verde
new ContainerBuilder().setAccentColor(0xED4245); // rojo
new ContainerBuilder().setAccentColor(0xFF8C00); // naranja
```

## Spoiler container

```typescript
const container = new ContainerBuilder()
    .setSpoiler(true)
    .addTextDisplayComponents(
        new TextDisplayBuilder().setContent("Contenido oculto!"),
    );
```

## Multiples componentes

```typescript
import { ContainerBuilder, TextDisplayBuilder, SeparatorBuilder, SeparatorSpacingSize } from 'discord.js';

const container = new ContainerBuilder()
    .setAccentColor(0x5865F2)
    .addTextDisplayComponents(
        new TextDisplayBuilder().setContent("### Titulo"),
    )
    .addSeparatorComponents(
        new SeparatorBuilder().setDivider(true).setSpacing(SeparatorSpacingSize.Small),
    )
    .addTextDisplayComponents(
        new TextDisplayBuilder().setContent("Texto aqui."),
    );
```

## Notas

- Un mensaje puede tener **multiples** containers en el primer nivel
- Containers pueden contener: `TextDisplayBuilder`, `SeparatorBuilder`, `SectionBuilder`, `MediaGalleryBuilder`, `FileBuilder`, `ActionRowBuilder`
- Containers **NO** pueden anidarse dentro de otros containers
