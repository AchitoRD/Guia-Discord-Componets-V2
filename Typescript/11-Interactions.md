# 11 - Interactions

Cada clic en boton o seleccion en menu dispara una interaccion.

## Responder a interacciones

Toda interaccion requiere respuesta dentro de **3 segundos**.

### Metodos

```typescript
await interaction.reply({ content: "mensaje" });
await interaction.reply({ content: "mensaje", ephemeral: true });
await interaction.deferReply();
await interaction.deferReply({ ephemeral: true });
```

### update() - editar el mensaje del componente

```typescript
await interaction.update({
    components: [newContainer],
    flags: MessageFlags.IsComponentsV2,
});
```

### deferUpdate()

```typescript
await interaction.deferUpdate();
// ... trabajo ...
await interaction.editReply({
    components: [newContainer],
    flags: MessageFlags.IsComponentsV2,
});
```

## awaitMessageComponent - esperar una respuesta

```typescript
const response = await interaction.reply({
    content: "Estas seguro?",
    components: [row],
    withResponse: true,
});

const filter = (i: Interaction) => i.user.id === interaction.user.id;

try {
    const confirmation = await response.resource.message.awaitMessageComponent({
        filter,
        time: 60_000,
    });

    if (confirmation.customId === "confirm") {
        await confirmation.update({ content: "Hecho!", components: [] });
    } else {
        await confirmation.update({ content: "Cancelado.", components: [] });
    }
} catch {
    await interaction.editReply({
        content: "Tiempo agotado",
        components: [],
    });
}
```

## InteractionCollector - multiples interacciones

```typescript
const response = await interaction.reply({
    content: "Elige!",
    components: [row],
    withResponse: true,
});

const collector = response.resource.message.createMessageComponentCollector({
    componentType: ComponentType.StringSelect,
    time: 3_600_000,
});

collector.on("collect", async (i) => {
    const selection = i.values[0];
    await i.reply({ content: `Seleccionaste ${selection}!`, ephemeral: true });
});
```

## Manejador global interactionCreate

```typescript
import { Events } from 'discord.js';

client.on(Events.InteractionCreate, async interaction => {
    if (interaction.isChatInputCommand()) {
        // manejar comando
    } else if (interaction.isButton()) {
        // manejar boton
    } else if (interaction.isStringSelectMenu()) {
        // manejar select menu
    } else if (interaction.isModalSubmit()) {
        // manejar modal
    }
});
```

## Notas

- Botones Link no disparan interacciones
- update() no puede cambiar el estado ephemeral
- deferUpdate() reconoce sin enviar contenido nuevo
