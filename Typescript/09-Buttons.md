# 09 - Buttons

Los botones van dentro de un `ActionRowBuilder<MessageActionRowComponentBuilder>`.

## Estilos

```typescript
import { ButtonBuilder, ButtonStyle } from 'discord.js';

new ButtonBuilder().setLabel("Primary").setStyle(ButtonStyle.Primary).setCustomId("btn_1");
new ButtonBuilder().setLabel("Secondary").setStyle(ButtonStyle.Secondary).setCustomId("btn_2");
new ButtonBuilder().setLabel("Success").setStyle(ButtonStyle.Success).setCustomId("btn_3");
new ButtonBuilder().setLabel("Danger").setStyle(ButtonStyle.Danger).setCustomId("btn_4");
new ButtonBuilder().setLabel("Link").setStyle(ButtonStyle.Link).setURL("https://discord.gg/aerox");
```

`ButtonStyle.Link` abre URL - no dispara interaccion, no necesita `custom_id`.

## Boton en mensaje CV2

```typescript
import { ContainerBuilder, TextDisplayBuilder, ActionRowBuilder, ButtonBuilder, ButtonStyle, type MessageActionRowComponentBuilder, MessageFlags } from 'discord.js';

const container = new ContainerBuilder()
    .addTextDisplayComponents(
        new TextDisplayBuilder().setContent("Presiona el boton:"),
    )
    .addActionRowComponents(
        new ActionRowBuilder<MessageActionRowComponentBuilder>()
            .addComponents(
                new ButtonBuilder()
                    .setLabel("Click Me")
                    .setStyle(ButtonStyle.Primary)
                    .setCustomId("my_button"),
            ),
    );

await interaction.reply({
    components: [container],
    flags: MessageFlags.IsComponentsV2,
});
```

## Manejar el clic

```typescript
client.on("interactionCreate", async interaction => {
    if (!interaction.isButton()) return;

    if (interaction.customId === "my_button") {
        await interaction.reply({ content: "Clicked!", ephemeral: true });
    }
});
```

## Boton deshabilitado

```typescript
new ButtonBuilder()
    .setLabel("No disponible")
    .setStyle(ButtonStyle.Secondary)
    .setCustomId("disabled")
    .setDisabled(true);
```

## Boton con emoji

```typescript
new ButtonBuilder()
    .setLabel("Aprobar")
    .setStyle(ButtonStyle.Success)
    .setCustomId("approve")
    .setEmoji("✅");

// Emoji personalizado:
new ButtonBuilder()
    .setLabel("Boost")
    .setStyle(ButtonStyle.Primary)
    .setCustomId("boost")
    .setEmoji({ id: "1234567890", name: "boost" });
```

## Boton como accesorio de Section

```typescript
const section = new SectionBuilder()
    .addTextDisplayComponents(
        new TextDisplayBuilder().setContent("Abre un ticket con el boton"),
    )
    .setButtonAccessory(
        new ButtonBuilder()
            .setLabel("Abrir Ticket")
            .setStyle(ButtonStyle.Primary)
            .setCustomId("open_ticket"),
    );
```

## Actualizar mensaje tras clic

```typescript
client.on("interactionCreate", async interaction => {
    if (!interaction.isButton() || interaction.customId !== "confirm") return;

    const confirmed = new ContainerBuilder()
        .addTextDisplayComponents(
            new TextDisplayBuilder().setContent("Confirmado!"),
        );

    await interaction.update({
        components: [confirmed],
        flags: MessageFlags.IsComponentsV2,
    });
});
```

## Notas

- Maximo **5 botones** por `ActionRow`
- `custom_id` obligatorio excepto `ButtonStyle.Link`
- `custom_id` maximo **100 caracteres**
- Botones persistentes: solo registra el handler en `interactionCreate`
