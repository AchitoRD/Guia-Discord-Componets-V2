# 11. Manejo de Interacciones

Cada clic en un botón o selección en un menú desplegable de un componente enviado por tu bot dispara una `interaction`, activando el evento `Client#interactionCreate`.

---

## Opciones de Manejo

- Esperar una sola interacción mediante `awaitMessageComponent()` en un canal o mensaje
- Escuchar múltiples interacciones durante un período usando un `InteractionCollector`
- Crear un manejador de componentes permanente en el evento `Client#interactionCreate`

## Responder a Interacciones de Componentes

Como con todas las interacciones, requieren una respuesta dentro de **3 segundos**, o Discord las tratará como fallidas.

Soportan los métodos `reply()`, `deferReply()`, `editReply()` y `followUp()`, con la opción de respuestas efímeras.

### update()

Reconoce la interacción editando el mensaje en el que estaba adjunto el componente.

```js
await interaction.update({
  components: [newContainer],
  flags: MessageFlags.IsComponentsV2,
});
```

### Deferred Updates

Reconoce la interacción y restablece el estado del mensaje.

```js
await interaction.deferUpdate();
// ... hacer trabajo ...
await interaction.editReply({
  components: [newContainer],
  flags: MessageFlags.IsComponentsV2,
});
```

## Esperar Componentes (awaitMessageComponent)

Para situaciones donde esperas una sola respuesta:

```js
const response = await interaction.reply({
  content: '¿Estás seguro?',
  components: [row],
  withResponse: true,
});

const collectorFilter = (i) => i.user.id === interaction.user.id;

try {
  const confirmation = await response.resource.message.awaitMessageComponent({
    filter: collectorFilter,
    time: 60_000,
  });

  if (confirmation.customId === 'confirm') {
    await confirmation.update({ content: '¡Acción realizada!', components: [] });
  } else if (confirmation.customId === 'cancel') {
    await confirmation.update({ content: 'Acción cancelada', components: [] });
  }
} catch {
  await interaction.editReply({
    content: 'No se recibió confirmación en 1 minuto, cancelando',
    components: [],
  });
}
```

## Collectores de Componentes

Para recopilar múltiples interacciones durante un período:

```js
const response = await interaction.reply({
  content: '¡Elige!',
  components: [row],
  withResponse: true,
});

const collector = response.resource.message.createMessageComponentCollector({
  componentType: ComponentType.StringSelect,
  time: 3_600_000, // 1 hora
});

collector.on('collect', async (i) => {
  const selection = i.values[0];
  await i.reply(`${i.user} seleccionó ${selection}!`);
});
```

## El Evento interactionCreate

Para manejadores permanentes de botones o menús desplegables:

```js
const { Events } = require('discord.js');

module.exports = {
  name: Events.InteractionCreate,
  async execute(interaction) {
    if (interaction.isChatInputCommand()) {
      const command = interaction.client.commands.get(interaction.commandName);
      if (!command) {
        console.error(`No se encontró el comando ${interaction.commandName}`);
        return;
      }
      try {
        await command.execute(interaction);
      } catch (error) {
        console.error(`Error ejecutando ${interaction.commandName}`);
        console.error(error);
      }
    } else if (interaction.isButton()) {
      // responder al botón
    } else if (interaction.isStringSelectMenu()) {
      // responder al menú desplegable
    }
  },
};
```
