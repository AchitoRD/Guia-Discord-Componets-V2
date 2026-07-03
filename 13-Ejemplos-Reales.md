# 13. Ejemplos del Mundo Real

Patrones completos y funcionales para casos de uso comunes de CV2 en Discord.js.

---

## 1. Notificación Efímera Simple

```js
const { ContainerBuilder, TextDisplayBuilder, MessageFlags } = require('discord.js');

const container = new ContainerBuilder()
  .addTextDisplayComponents(
    new TextDisplayBuilder().setContent('Estás en la lista negra para crear tickets.')
  );

await interaction.reply({
  components: [container],
  flags: MessageFlags.IsComponentsV2 | MessageFlags.Ephemeral,
});
```

## 2. Contenedor de Información con Título + Cuerpo

```js
const { ContainerBuilder, TextDisplayBuilder, SeparatorBuilder, SeparatorSpacingSize, MessageFlags } = require('discord.js');

const container = new ContainerBuilder()
  .addTextDisplayComponents(
    new TextDisplayBuilder().setContent('### Ticket Reabierto')
  )
  .addSeparatorComponents(
    new SeparatorBuilder().setDivider(true).setSpacing(SeparatorSpacingSize.Small)
  )
  .addTextDisplayComponents(
    new TextDisplayBuilder().setContent(`Reabierto por <@${interaction.user.id}>`)
  );

await interaction.reply({
  components: [container],
  flags: MessageFlags.IsComponentsV2,
});
```

## 3. Panel de Control de Ticket

```js
const { ContainerBuilder, TextDisplayBuilder, SeparatorBuilder, SeparatorSpacingSize,
  SectionBuilder, ThumbnailBuilder, ActionRowBuilder, ButtonBuilder, ButtonStyle, MessageFlags } = require('discord.js');

const container = new ContainerBuilder()
  .setAccentColor(0x5865F2)
  .addTextDisplayComponents(
    new TextDisplayBuilder().setContent('# Ticket de Soporte')
  )
  .addSeparatorComponents(
    new SeparatorBuilder().setDivider(true).setSpacing(SeparatorSpacingSize.Small)
  )
  .addSectionComponents(
    new SectionBuilder()
      .addTextDisplayComponents(
        new TextDisplayBuilder().setContent(
          `**Bienvenido** <@${user.id}>\n**Categoría:** ${category}\n\nNuestro equipo te atenderá en breve.`
        )
      )
      .setThumbnailAccessory(
        new ThumbnailBuilder().setURL(user.displayAvatarURL())
      )
  )
  .addSeparatorComponents(
    new SeparatorBuilder().setDivider(true).setSpacing(SeparatorSpacingSize.Small)
  )
  .addActionRowComponents(
    new ActionRowBuilder().addComponents(
      new ButtonBuilder().setLabel('Tomar').setStyle(ButtonStyle.Primary).setCustomId('ticket_claim'),
      new ButtonBuilder().setLabel('Cerrar').setStyle(ButtonStyle.Danger).setCustomId('ticket_close')
    )
  );

await channel.send({
  components: [container],
  flags: MessageFlags.IsComponentsV2,
});
```

## 4. Panel con Menú Desplegable

```js
const { StringSelectMenuBuilder, StringSelectMenuOptionBuilder } = require('discord.js');

const options = categories.map(name =>
  new StringSelectMenuOptionBuilder().setLabel(name).setValue(name)
);

const container = new ContainerBuilder()
  .setAccentColor(0x5865F2)
  .addSectionComponents(
    new SectionBuilder()
      .addTextDisplayComponents(
        new TextDisplayBuilder().setContent(`**__${title}__**\n\n${description}`)
      )
      .setThumbnailAccessory(
        new ThumbnailBuilder().setURL(interaction.guild.iconURL())
      )
  )
  .addSeparatorComponents(
    new SeparatorBuilder().setDivider(true).setSpacing(SeparatorSpacingSize.Small)
  )
  .addActionRowComponents(
    new ActionRowBuilder().addComponents(
      new StringSelectMenuBuilder()
        .setCustomId('ticket_category_select')
        .setPlaceholder('Selecciona una categoría...')
        .addOptions(...options)
    )
  );

await channel.send({
  components: [container],
  flags: MessageFlags.IsComponentsV2,
});
```

## 5. Log con Archivo Adjunto

```js
const { AttachmentBuilder, FileBuilder } = require('discord.js');

const filename = `ticket-${String(ticketNumber).padStart(4, '0')}-transcript.txt`;
const file = new AttachmentBuilder('./transcripts/' + filename).setName(filename);

const container = new ContainerBuilder()
  .addTextDisplayComponents(
    new TextDisplayBuilder().setContent('### Logs - Ticket Eliminado')
  )
  .addSeparatorComponents(
    new SeparatorBuilder().setDivider(true).setSpacing(SeparatorSpacingSize.Small)
  )
  .addTextDisplayComponents(
    new TextDisplayBuilder().setContent(
      `Ticket \`#${String(ticketNumber).padStart(4, '0')}\` eliminado por ${interaction.user.displayName}\n**Categoría:** ${category}`
    )
  )
  .addSeparatorComponents(
    new SeparatorBuilder().setDivider(true).setSpacing(SeparatorSpacingSize.Small)
  )
  .addFileComponents(
    new FileBuilder().setURL(`attachment://${filename}`)
  );

await logChannel.send({
  components: [container],
  files: [file],
  flags: MessageFlags.IsComponentsV2,
});
```

## 6. Avatar con MediaGallery

```js
const avatarUrl = user.displayAvatarURL({ size: 1024, extension: 'png' });

const container = new ContainerBuilder()
  .addTextDisplayComponents(
    new TextDisplayBuilder().setContent(`### Avatar de ${user.displayName}`)
  )
  .addSeparatorComponents(
    new SeparatorBuilder().setDivider(true).setSpacing(SeparatorSpacingSize.Small)
  )
  .addMediaGalleryComponents(
    new MediaGalleryBuilder().addItems(
      new MediaGalleryItemBuilder().setURL(avatarUrl).setDescription('Avatar de usuario')
    )
  )
  .addSeparatorComponents(
    new SeparatorBuilder().setDivider(false).setSpacing(SeparatorSpacingSize.Small)
  )
  .addTextDisplayComponents(
    new TextDisplayBuilder().setContent(`[PNG](${avatarUrl}) | [WebP](${user.displayAvatarURL({ extension: 'webp' })})`)
  );

await interaction.reply({
  components: [container],
  flags: MessageFlags.IsComponentsV2 | MessageFlags.Ephemeral,
});
```

## 7. Comando Slash Completo

```js
const { SlashCommandBuilder, MessageFlags, ContainerBuilder, TextDisplayBuilder,
  SeparatorBuilder, SeparatorSpacingSize, SectionBuilder, ThumbnailBuilder,
  MediaGalleryBuilder, MediaGalleryItemBuilder, ActionRowBuilder, ButtonBuilder, ButtonStyle } = require('discord.js');

module.exports = {
  data: new SlashCommandBuilder().setName('showcase').setDescription('Demostración CV2'),

  async execute(interaction) {
    const avatar = interaction.client.user.displayAvatarURL({ extension: 'png', size: 512 });

    const container = new ContainerBuilder()
      .setAccentColor(0x5865F2)
      .addTextDisplayComponents(
        new TextDisplayBuilder().setContent('# Demostración de Components V2')
      )
      .addSeparatorComponents(
        new SeparatorBuilder().setDivider(true).setSpacing(SeparatorSpacingSize.Small)
      )
      .addSectionComponents(
        new SectionBuilder()
          .addTextDisplayComponents(
            new TextDisplayBuilder().setContent('**Info del Bot**'),
            new TextDisplayBuilder().setContent('Miniatura junto al texto.')
          )
          .setThumbnailAccessory(
            new ThumbnailBuilder().setURL(avatar).setDescription('Avatar del bot')
          )
      )
      .addSeparatorComponents(
        new SeparatorBuilder().setDivider(true).setSpacing(SeparatorSpacingSize.Small)
      )
      .addMediaGalleryComponents(
        new MediaGalleryBuilder().addItems(
          new MediaGalleryItemBuilder().setURL('https://picsum.photos/seed/a/400/300').setDescription('Imagen 1'),
          new MediaGalleryItemBuilder().setURL('https://picsum.photos/seed/b/400/300').setDescription('Imagen 2')
        )
      )
      .addSeparatorComponents(
        new SeparatorBuilder().setDivider(true).setSpacing(SeparatorSpacingSize.Small)
      )
      .addActionRowComponents(
        new ActionRowBuilder().addComponents(
          new ButtonBuilder().setLabel('Haz Clic').setStyle(ButtonStyle.Primary).setCustomId('demo_btn')
        )
      )
      .addTextDisplayComponents(
        new TextDisplayBuilder().setContent('-# Desarrollado con Components V2')
      );

    await interaction.reply({
      components: [container],
      flags: MessageFlags.IsComponentsV2,
    });
  },
};
```

## 8. Paginación

```js
function buildPage(items, page = 0, pageSize = 5) {
  const chunk = items.slice(page * pageSize, (page + 1) * pageSize);

  const container = new ContainerBuilder()
    .addTextDisplayComponents(
      new TextDisplayBuilder().setContent(`# Página ${page + 1}`)
    )
    .addSeparatorComponents(
      new SeparatorBuilder().setDivider(true).setSpacing(SeparatorSpacingSize.Small)
    );

  chunk.forEach(item => {
    container.addTextDisplayComponents(
      new TextDisplayBuilder().setContent(`**${item.title}**\n${item.desc}`)
    );
  });

  const buttons = [];
  if (page > 0)
    buttons.push(new ButtonBuilder().setCustomId('prev').setLabel('Anterior').setStyle(ButtonStyle.Secondary));
  if ((page + 1) * pageSize < items.length)
    buttons.push(new ButtonBuilder().setCustomId('next').setLabel('Siguiente').setStyle(ButtonStyle.Secondary));

  if (buttons.length)
    container.addActionRowComponents(new ActionRowBuilder().addComponents(...buttons));

  return container;
}
```

## 9. Modal con FileUploadBuilder + MediaGallery (flujo completo)

El usuario sube una imagen mediante un modal y se muestra inmediatamente en una galería CV2.

```js
const { SlashCommandBuilder, ModalBuilder, LabelBuilder, FileUploadBuilder, TextInputBuilder,
  TextInputStyle, ContainerBuilder, TextDisplayBuilder, MediaGalleryBuilder,
  MediaGalleryItemBuilder, MessageFlags, Events } = require('discord.js');

module.exports = {
  data: new SlashCommandBuilder()
    .setName('publicar')
    .setDescription('Publica una imagen con título'),

  async execute(interaction) {
    const modal = new ModalBuilder().setCustomId('pub_modal').setTitle('Publicar imagen');

    const tituloInput = new TextInputBuilder()
      .setCustomId('titulo')
      .setStyle(TextInputStyle.Short)
      .setMaxLength(100);

    const tituloLabel = new LabelBuilder()
      .setLabel('Título de la imagen')
      .setTextInputComponent(tituloInput);

    const fileUpload = new FileUploadBuilder()
      .setCustomId('imagen')
      .setMaxValues(1);

    const fileLabel = new LabelBuilder()
      .setLabel('Selecciona una imagen')
      .setFileUploadComponent(fileUpload);

    modal.addLabelComponents(tituloLabel, fileLabel);
    await interaction.showModal(modal);
  },
};

// Manejador del modal (en events/interactionCreate.js o similar):
client.on(Events.InteractionCreate, async (interaction) => {
  if (!interaction.isModalSubmit() || interaction.customId !== 'pub_modal') return;

  await interaction.deferReply({ ephemeral: true });

  const titulo = interaction.fields.getTextInputValue('titulo');
  const archivos = interaction.fields.getUploadedFiles('imagen');

  const container = new ContainerBuilder()
    .setAccentColor(0x5865F2)
    .addTextDisplayComponents(
      new TextDisplayBuilder().setContent(`### ${titulo}`)
    );

  if (archivos?.size) {
    container.addMediaGalleryComponents(
      new MediaGalleryBuilder().addItems(
        new MediaGalleryItemBuilder()
          .setURL(archivos.first().url) // ✅ URL directa de la CDN
          .setDescription(titulo)
      )
    );
  }

  await interaction.editReply({
    components: [container],
    flags: MessageFlags.IsComponentsV2,
  });
});
```
