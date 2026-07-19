# 08 - ActionRow

`ActionRowBuilder` coloca **botones y selects** dentro de un layout CV2.

## Botones en un container

```typescript
import { ContainerBuilder, ActionRowBuilder, ButtonBuilder, ButtonStyle, type MessageActionRowComponentBuilder, MessageFlags } from 'discord.js';

const container = new ContainerBuilder()
    .addActionRowComponents(
        new ActionRowBuilder<MessageActionRowComponentBuilder>()
            .addComponents(
                new ButtonBuilder().setLabel("Confirmar").setStyle(ButtonStyle.Success).setCustomId("confirm"),
                new ButtonBuilder().setLabel("Cancelar").setStyle(ButtonStyle.Danger).setCustomId("cancel"),
            ),
    );
```

## Select menu en un container

```typescript
import { StringSelectMenuBuilder, StringSelectMenuOptionBuilder, ActionRowBuilder, ContainerBuilder, type MessageActionRowComponentBuilder, MessageFlags } from 'discord.js';

const select = new StringSelectMenuBuilder()
    .setCustomId("category_select")
    .setPlaceholder("Elige una categoria...")
    .addOptions(
        new StringSelectMenuOptionBuilder().setLabel("General").setValue("general"),
        new StringSelectMenuOptionBuilder().setLabel("Facturacion").setValue("billing"),
    );

const container = new ContainerBuilder()
    .addActionRowComponents(
        new ActionRowBuilder<MessageActionRowComponentBuilder>()
            .addComponents(select),
    );
```

## Hasta 5 botones por fila

```typescript
const buttons = categorias.slice(0, 5).map(name =>
    new ButtonBuilder().setLabel(name).setStyle(ButtonStyle.Primary).setCustomId(`btn_${name}`),
);

const container = new ContainerBuilder()
    .addActionRowComponents(
        new ActionRowBuilder<MessageActionRowComponentBuilder>()
            .addComponents(...buttons),
    );
```

Para mas de 5, usa multiples action rows:

```typescript
for (let i = 0; i < buttons.length; i += 5) {
    container.addActionRowComponents(
        new ActionRowBuilder<MessageActionRowComponentBuilder>()
            .addComponents(...buttons.slice(i, i + 5)),
    );
}
```

## Notas

- Cada `ActionRow` soporta **5 botones** o **1 select menu**
- `custom_id` es obligatorio en botones y selects (excepto `ButtonStyle.Link`)
- El generic `<MessageActionRowComponentBuilder>` es necesario en TypeScript
