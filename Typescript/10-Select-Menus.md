# 10 - Select Menus

Los menus desplegables van dentro de un `ActionRowBuilder<MessageActionRowComponentBuilder>`. Un select por fila.

## Tipos

| Builder | Funcion |
|---------|---------|
| `StringSelectMenuBuilder` | Opciones personalizadas |
| `UserSelectMenuBuilder` | Elegir usuario |
| `RoleSelectMenuBuilder` | Elegir rol |
| `ChannelSelectMenuBuilder` | Elegir canal |
| `MentionableSelectMenuBuilder` | Elegir usuario o rol |

## String Select Menu

```typescript
import { StringSelectMenuBuilder, StringSelectMenuOptionBuilder, ActionRowBuilder, ContainerBuilder, TextDisplayBuilder, type MessageActionRowComponentBuilder, MessageFlags } from 'discord.js';

const select = new StringSelectMenuBuilder()
    .setCustomId("category_select")
    .setPlaceholder("Elige una categoria...")
    .addOptions(
        new StringSelectMenuOptionBuilder().setLabel("General").setValue("general").setDescription("Soporte general"),
        new StringSelectMenuOptionBuilder().setLabel("Facturacion").setValue("billing").setDescription("Pagos"),
        new StringSelectMenuOptionBuilder().setLabel("Tecnico").setValue("technical").setDescription("Ayuda tecnica"),
    );

const container = new ContainerBuilder()
    .addTextDisplayComponents(
        new TextDisplayBuilder().setContent("Selecciona una categoria:"),
    )
    .addActionRowComponents(
        new ActionRowBuilder<MessageActionRowComponentBuilder>()
            .addComponents(select),
    );

await interaction.reply({
    components: [container],
    flags: MessageFlags.IsComponentsV2,
});
```

## Manejar seleccion

```typescript
client.on("interactionCreate", async interaction => {
    if (!interaction.isStringSelectMenu()) return;

    if (interaction.customId === "category_select") {
        const chosen = interaction.values[0];
        await interaction.reply({ content: `Seleccionaste: ${chosen}`, ephemeral: true });
    }
});
```

## Seleccion multiple

```typescript
new StringSelectMenuBuilder()
    .setCustomId("role_picker")
    .setPlaceholder("Elige hasta 3 roles")
    .setMinValues(1)
    .setMaxValues(3)
    .addOptions(
        new StringSelectMenuOptionBuilder().setLabel("Anuncios").setValue("ann"),
        new StringSelectMenuOptionBuilder().setLabel("Updates").setValue("upd"),
        new StringSelectMenuOptionBuilder().setLabel("Eventos").setValue("evt"),
    );
```

`interaction.values` es un array de los valores seleccionados.

## User Select Menu

```typescript
import { UserSelectMenuBuilder, ActionRowBuilder, type MessageActionRowComponentBuilder } from 'discord.js';

const select = new UserSelectMenuBuilder()
    .setCustomId("user_picker")
    .setPlaceholder("Elige un usuario...");

client.on("interactionCreate", async interaction => {
    if (!interaction.isUserSelectMenu()) return;
    const user = interaction.users.first();
    await interaction.reply({ content: `Elegiste a ${user}`, ephemeral: true });
});
```

## Channel Select Menu

```typescript
import { ChannelSelectMenuBuilder, ChannelType, ActionRowBuilder, type MessageActionRowComponentBuilder } from 'discord.js';

const select = new ChannelSelectMenuBuilder()
    .setCustomId("channel_picker")
    .setPlaceholder("Elige un canal...")
    .addChannelTypes(ChannelType.GuildText);

client.on("interactionCreate", async interaction => {
    if (!interaction.isChannelSelectMenu()) return;
    const channel = interaction.channels.first();
    await interaction.reply({ content: `Canal: ${channel}`, ephemeral: true });
});
```

## Notas

- Solo **1** select menu por `ActionRow`
- Hasta **25 opciones** en `StringSelectMenuBuilder`
- `custom_id` maximo **100 caracteres**
- Usa `interaction.values` para strings; `interaction.users/roles/channels` para entidades
