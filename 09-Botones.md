# 9. Botones

Los botones son componentes interactivos colocados dentro de un `ActionRowBuilder`. Cuando se hace clic en ellos, disparan un evento `interactionCreate` que tu bot maneja.

---

## Estilos de Botón

```js
const { ButtonBuilder, ButtonStyle } = require('discord.js');

new ButtonBuilder().setLabel('Primario').setStyle(ButtonStyle.Primary).setCustomId('btn_primary')
new ButtonBuilder().setLabel('Secundario').setStyle(ButtonStyle.Secondary).setCustomId('btn_secondary')
new ButtonBuilder().setLabel('Éxito').setStyle(ButtonStyle.Success).setCustomId('btn_success')
new ButtonBuilder().setLabel('Peligro').setStyle(ButtonStyle.Danger).setCustomId('btn_danger')
new ButtonBuilder().setLabel('Enlace').setStyle(ButtonStyle.Link).setURL('https://discord.gg/aerox')
```

> `ButtonStyle.Link` abre una URL — **no** dispara una interacción. No necesita `custom_id`.

## Botón Básico en un Mensaje CV2

```js
const { ContainerBuilder, TextDisplayBuilder, ActionRowBuilder, ButtonBuilder, ButtonStyle, MessageFlags } = require('discord.js');

const container = new ContainerBuilder()
  .addTextDisplayComponents(
    new TextDisplayBuilder().setContent('Presiona el botón de abajo.')
  )
  .addActionRowComponents(
    new ActionRowBuilder().addComponents(
      new ButtonBuilder()
        .setLabel('Haz Clic')
        .setStyle(ButtonStyle.Primary)
        .setCustomId('my_button')
    )
  );

await interaction.reply({
  components: [container],
  flags: MessageFlags.IsComponentsV2,
});
```

## Manejar el Clic

```js
client.on('interactionCreate', async interaction => {
  if (!interaction.isButton()) return;

  if (interaction.customId === 'my_button') {
    await interaction.reply({ content: '¡Hiciste clic!', ephemeral: true });
  }
});
```

## Múltiples Botones en una Fila

```js
new ActionRowBuilder().addComponents(
  new ButtonBuilder().setLabel('Aceptar').setStyle(ButtonStyle.Success).setCustomId('accept'),
  new ButtonBuilder().setLabel('Rechazar').setStyle(ButtonStyle.Danger).setCustomId('decline'),
  new ButtonBuilder().setLabel('Info').setStyle(ButtonStyle.Secondary).setCustomId('info')
)
```

Hasta **5 botones** por fila. Para más, añade otro `ActionRowBuilder`.

## Botón Deshabilitado

```js
new ButtonBuilder()
  .setLabel('No disponible')
  .setStyle(ButtonStyle.Secondary)
  .setCustomId('disabled_btn')
  .setDisabled(true)
```

## Botón con Emoji

```js
new ButtonBuilder()
  .setLabel('Aprobar')
  .setStyle(ButtonStyle.Success)
  .setCustomId('approve')
  .setEmoji('✅')

// Emoji personalizado de Discord:
new ButtonBuilder()
  .setLabel('Impulsar')
  .setStyle(ButtonStyle.Primary)
  .setCustomId('boost')
  .setEmoji({ id: '1234567890', name: 'boost' })
```

## Botón como Accesorio de Section

Un botón puede situarse **en línea junto al texto** dentro de un `SectionBuilder` — sin necesidad de `ActionRow`:

```js
const { SectionBuilder, TextDisplayBuilder, ButtonBuilder, ButtonStyle } = require('discord.js');

const section = new SectionBuilder()
  .addTextDisplayComponents(
    new TextDisplayBuilder().setContent('Abre un ticket de soporte usando el botón →')
  )
  .setButtonAccessory(
    new ButtonBuilder()
      .setLabel('Abrir Ticket')
      .setStyle(ButtonStyle.Primary)
      .setCustomId('open_ticket')
  );
```

## Actualizar el Mensaje Después de un Clic

```js
client.on('interactionCreate', async interaction => {
  if (!interaction.isButton() || interaction.customId !== 'confirm') return;

  const confirmed = new ContainerBuilder()
    .addTextDisplayComponents(
      new TextDisplayBuilder().setContent('✅ ¡Confirmado!')
    );

  await interaction.update({
    components: [confirmed],
    flags: MessageFlags.IsComponentsV2,
  });
});
```

## Botones Persistentes

Para botones que deben seguir funcionando después de que el bot se reinicie, siempre establece un `custom_id` y asegúrate de que tu manejador `interactionCreate` esté registrado al iniciar.

```js
client.on('interactionCreate', async interaction => {
  if (!interaction.isButton()) return;
  // manejar por customId...
});
```

## Notas

- Cada `ActionRow` puede contener hasta **5 botones**
- `custom_id` es obligatorio en todos los botones excepto `ButtonStyle.Link`
- `custom_id` puede tener hasta **100 caracteres** — úsalo para almacenar estado (ej. `close_ticket_42`)
- Los botones Link no disparan interacciones — abren una URL directamente
