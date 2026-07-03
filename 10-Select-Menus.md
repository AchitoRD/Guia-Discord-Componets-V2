# 10. Menús Desplegables (Select Menus)

Los menús desplegables permiten a los usuarios elegir una o más opciones de una lista. Van dentro de un `ActionRowBuilder` — un select por fila.

---

## Tipos de Menús Desplegables

| Builder | Qué Hace |
|---------|----------|
| `StringSelectMenuBuilder` | Lista personalizada de opciones que tú defines |
| `UserSelectMenuBuilder` | Elegir un usuario del servidor |
| `RoleSelectMenuBuilder` | Elegir un rol |
| `ChannelSelectMenuBuilder` | Elegir un canal |
| `MentionableSelectMenuBuilder` | Elegir un usuario o rol |

## String Select Menu

El tipo más común — tú defines las opciones:

```js
const { StringSelectMenuBuilder, StringSelectMenuOptionBuilder, ActionRowBuilder, ContainerBuilder, TextDisplayBuilder, MessageFlags } = require('discord.js');

const select = new StringSelectMenuBuilder()
  .setCustomId('category_select')
  .setPlaceholder('Elige una categoría...')
  .addOptions(
    new StringSelectMenuOptionBuilder().setLabel('General').setValue('general').setDescription('Soporte general'),
    new StringSelectMenuOptionBuilder().setLabel('Facturación').setValue('billing').setDescription('Problemas de pago'),
    new StringSelectMenuOptionBuilder().setLabel('Técnico').setValue('technical').setDescription('Ayuda técnica')
  );

const container = new ContainerBuilder()
  .addTextDisplayComponents(
    new TextDisplayBuilder().setContent('Selecciona una categoría de soporte abajo.')
  )
  .addActionRowComponents(
    new ActionRowBuilder().addComponents(select)
  );

await interaction.reply({
  components: [container],
  flags: MessageFlags.IsComponentsV2,
});
```

## Manejar la Selección

```js
client.on('interactionCreate', async interaction => {
  if (!interaction.isStringSelectMenu()) return;

  if (interaction.customId === 'category_select') {
    const chosen = interaction.values[0];
    await interaction.reply({ content: `Seleccionaste: **${chosen}**`, ephemeral: true });
  }
});
```

## Selecciones Múltiples Permitidas

```js
new StringSelectMenuBuilder()
  .setCustomId('role_picker')
  .setPlaceholder('Elige hasta 3 roles')
  .setMinValues(1)
  .setMaxValues(3)
  .addOptions(
    new StringSelectMenuOptionBuilder().setLabel('Anuncios').setValue('ann'),
    new StringSelectMenuOptionBuilder().setLabel('Actualizaciones').setValue('upd'),
    new StringSelectMenuOptionBuilder().setLabel('Eventos').setValue('evt')
  )
```

`interaction.values` será un array de todos los valores seleccionados.

## Opción con Emoji y Valor por Defecto

```js
new StringSelectMenuOptionBuilder()
  .setLabel('Facturación')
  .setValue('billing')
  .setDescription('Problemas de pago y suscripción')
  .setEmoji('💳')
  .setDefault(false)
```

## User Select Menu

```js
const { UserSelectMenuBuilder } = require('discord.js');

const select = new UserSelectMenuBuilder()
  .setCustomId('user_picker')
  .setPlaceholder('Elige un usuario...');

client.on('interactionCreate', async interaction => {
  if (!interaction.isUserSelectMenu()) return;
  const pickedUser = interaction.users.first();
  await interaction.reply({ content: `Elegiste a ${pickedUser}`, ephemeral: true });
});
```

## Role Select Menu

```js
const { RoleSelectMenuBuilder } = require('discord.js');

const select = new RoleSelectMenuBuilder()
  .setCustomId('role_picker')
  .setPlaceholder('Elige un rol...');

client.on('interactionCreate', async interaction => {
  if (!interaction.isRoleSelectMenu()) return;
  const pickedRole = interaction.roles.first();
  await interaction.reply({ content: `Elegiste ${pickedRole}`, ephemeral: true });
});
```

## Channel Select Menu

```js
const { ChannelSelectMenuBuilder, ChannelType } = require('discord.js');

const select = new ChannelSelectMenuBuilder()
  .setCustomId('channel_picker')
  .setPlaceholder('Elige un canal...')
  .addChannelTypes(ChannelType.GuildText);
```

## Menú Desplegable Deshabilitado

```js
new StringSelectMenuBuilder()
  .setCustomId('disabled_select')
  .setPlaceholder('No disponible ahora')
  .setDisabled(true)
  .addOptions(
    new StringSelectMenuOptionBuilder().setLabel('Opción').setValue('x')
  )
```

## Actualizar el Mensaje Después de una Selección

```js
client.on('interactionCreate', async interaction => {
  if (!interaction.isStringSelectMenu() || interaction.customId !== 'category_select') return;

  const category = interaction.values[0];

  const updated = new ContainerBuilder()
    .addTextDisplayComponents(
      new TextDisplayBuilder().setContent(`**Categoría:** ${category}\n\nUn miembro del staff te atenderá en breve.`)
    );

  await interaction.update({
    components: [updated],
    flags: MessageFlags.IsComponentsV2,
  });
});
```

## Notas

- Solo **1 menú desplegable** por `ActionRow`
- Hasta **25 opciones** en un `StringSelectMenuBuilder`
- `custom_id` puede tener hasta **100 caracteres**
- Usa `interaction.values` para obtener los strings seleccionados; usa `interaction.users`, `interaction.roles` o `interaction.channels` para selects de entidades
