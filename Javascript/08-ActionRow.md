# 8. :bookmark_tabs: ActionRow

`ActionRowBuilder` te permite colocar **botones y menús desplegables** dentro de un diseño CV2. En CV2, las action rows van dentro de un `ContainerBuilder` o en el primer nivel del array `components`.

---

## Botones Dentro de un Container

```js
const { ContainerBuilder, ActionRowBuilder, ButtonBuilder, ButtonStyle, MessageFlags } = require('discord.js');

const container = new ContainerBuilder()
  .addActionRowComponents(
    new ActionRowBuilder().addComponents(
      new ButtonBuilder().setLabel('Confirmar').setStyle(ButtonStyle.Success).setCustomId('confirm'),
      new ButtonBuilder().setLabel('Cancelar').setStyle(ButtonStyle.Danger).setCustomId('cancel')
    )
  );
```

## Manejo de Clics en Botones

```js
client.on('interactionCreate', async interaction => {
  if (!interaction.isButton()) return;

  if (interaction.customId === 'confirm') {
    await interaction.reply({ content: '¡Confirmado!', ephemeral: true });
  }

  if (interaction.customId === 'cancel') {
    await interaction.reply({ content: 'Cancelado.', ephemeral: true });
  }
});
```

## Menú Desplegable Dentro de un Container

```js
const { StringSelectMenuBuilder, StringSelectMenuOptionBuilder, ActionRowBuilder, ContainerBuilder, MessageFlags } = require('discord.js');

const select = new StringSelectMenuBuilder()
  .setCustomId('category_select')
  .setPlaceholder('Elige una categoría...')
  .addOptions(
    new StringSelectMenuOptionBuilder().setLabel('General').setValue('general'),
    new StringSelectMenuOptionBuilder().setLabel('Facturación').setValue('billing')
  );

const container = new ContainerBuilder()
  .addActionRowComponents(
    new ActionRowBuilder().addComponents(select)
  );
```

## Hasta 5 Botones por Fila

```js
const buttons = categories.slice(0, 5).map(name =>
  new ButtonBuilder().setLabel(name).setStyle(ButtonStyle.Primary).setCustomId(`btn_${name}`)
);

const container = new ContainerBuilder()
  .addActionRowComponents(
    new ActionRowBuilder().addComponents(...buttons)
  );
```

Para más de 5 botones, añade múltiples action rows:

```js
for (let i = 0; i < buttons.length; i += 5) {
  container.addActionRowComponents(
    new ActionRowBuilder().addComponents(...buttons.slice(i, i + 5))
  );
}
```

## Notas

- Cada `ActionRow` puede contener hasta **5 botones** o **1 menú desplegable**
- `custom_id` es obligatorio en todos los botones y selects (excepto `ButtonStyle.Link`)
- Las interacciones de botones se manejan mediante el evento `interactionCreate`

---

> :book: **Guía original** por **itsfizys** — :link: [discordjs-components-v2-guide](https://github.com/itsfizys/discordjs-components-v2-guide)  
> :speech_balloon: **Soporte:** [discord.gg/aerox](https://discord.gg/aerox)
