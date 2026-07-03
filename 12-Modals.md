# 12. Modals (Ventanas Emergentes)

Los modals son ventanas emergentes que te permiten solicitar información adicional al usuario. Esta respuesta de interacción en forma de formulario bloquea al usuario de interactuar con Discord hasta que el modal se envía o se descarta.

> Esta sección complementa la guía de [comandos slash](https://discordjs.guide/legacy/slash-commands/advanced-creation).

---

## Construir y Responder con Modals

Con la clase `ModalBuilder`, discord.js ofrece una forma conveniente de construir modals paso a paso.

Puedes tener un máximo de **cinco** componentes de primer nivel por modal.

```js
const { Events, ModalBuilder } = require('discord.js');

client.on(Events.InteractionCreate, async (interaction) => {
  if (!interaction.isChatInputCommand()) return;
  if (interaction.commandName === 'ping') {
    const modal = new ModalBuilder().setCustomId('myModal').setTitle('Mi Modal');
    // TODO: Añadir componentes al modal...
  }
});
```

El `customId` es un string de hasta 100 caracteres que identifica de forma única esta instancia del modal.

## Label (Etiqueta)

Los componentes Label envuelven a otros componentes del modal para añadir contexto.

```js
const { LabelBuilder, ModalBuilder } = require('discord.js');

const modal = new ModalBuilder().setCustomId('myModal').setTitle('Mi Modal');

const hobbiesLabel = new LabelBuilder()
  .setLabel('¿Cuáles son tus pasatiempos favoritos?')
  .setDescription('Actividades que te gusta hacer');

modal.addLabelComponents(hobbiesLabel);
```

El campo `label` tiene un máximo de **45 caracteres**. El campo `description` tiene un máximo de **100 caracteres**.

## Text Input (Entrada de Texto)

```js
const { LabelBuilder, ModalBuilder, TextInputBuilder, TextInputStyle } = require('discord.js');

const modal = new ModalBuilder().setCustomId('myModal').setTitle('Mi Modal');

const hobbiesInput = new TextInputBuilder()
  .setCustomId('hobbiesInput')
  .setStyle(TextInputStyle.Short) // Short = una sola línea
  .setPlaceholder('juegos de cartas, películas, libros, etc.');

const hobbiesLabel = new LabelBuilder()
  .setLabel('¿Cuáles son tus pasatiempos favoritos?')
  .setDescription('Actividades que te gusta hacer')
  .setTextInputComponent(hobbiesInput);

modal.addLabelComponents(hobbiesLabel);
```

### Estilos de Entrada

- `Short` — entrada de texto de una sola línea
- `Paragraph` — entrada de texto de múltiples líneas

### Propiedades de Entrada

```js
const input = new TextInputBuilder()
  .setId(1)               // ID numérico para identificar el componente
  .setMaxLength(1_000)    // Máximo de caracteres
  .setMinLength(10)       // Mínimo de caracteres requerido
  .setValue('Por defecto') // Valor por defecto
  .setRequired(true);     // Campo obligatorio (default: true)
```

## Select Menu en Modal

```js
const { StringSelectMenuBuilder, StringSelectMenuOptionBuilder } = require('discord.js');

const favoriteStarterSelect = new StringSelectMenuBuilder()
  .setCustomId('starter')
  .setPlaceholder('¡Haz una selección!')
  .setRequired(true)
  .addOptions(
    new StringSelectMenuOptionBuilder()
      .setLabel('Bulbasaur').setDescription('Tipo Planta/Veneno.').setValue('bulbasaur'),
    new StringSelectMenuOptionBuilder()
      .setLabel('Charmander').setDescription('Tipo Fuego.').setValue('charmander'),
    new StringSelectMenuOptionBuilder()
      .setLabel('Squirtle').setDescription('Tipo Agua.').setValue('squirtle'),
  );

const favoriteStarterLabel = new LabelBuilder()
  .setLabel('¿Cuál es tu inicial favorito?')
  .setStringSelectMenuComponent(favoriteStarterSelect);

modal.addLabelComponents(favoriteStarterLabel);
```

## Text Display en Modal

```js
const text = new TextDisplayBuilder().setContent(
  'Texto que no cabía en una etiqueta\n-# También se puede usar Markdown',
);

modal.addTextDisplayComponents(text);
```

## File Upload en Modal

```js
const { FileUploadBuilder } = require('discord.js');

const upload = new FileUploadBuilder()
  .setCustomId('picture')
  .setId(1)
  .setMinValues(1)
  .setMaxValues(1)
  .setRequired(true);

const label = new LabelBuilder()
  .setLabel('Foto de la Semana')
  .setDescription('Las mejores fotos que has tomado esta semana')
  .setFileUploadComponent(upload);

modal.addLabelComponents(label);
```

> Discord **no envía los datos del archivo** en la interacción. Debes descargarlo desde la CDN de Discord. Sin embargo, para usar la imagen en un `MediaGalleryBuilder`, la URL de la CDN funciona directamente (ver sección más abajo).

## Responder con un Modal

Mostrar un modal debe ser la **primera respuesta** a una interacción. **No puedes** hacer defer.

```js
client.on(Events.InteractionCreate, async (interaction) => {
  if (!interaction.isChatInputCommand()) return;
  if (interaction.commandName === 'mimodal') {
    const modal = new ModalBuilder().setCustomId('miModal').setTitle('Formulario');

    const input = new TextInputBuilder()
      .setCustomId('nombre')
      .setStyle(TextInputStyle.Short)
      .setMaxLength(50);

    const label = new LabelBuilder()
      .setLabel('Nombre completo')
      .setTextInputComponent(input);

    modal.addLabelComponents(label);
    await interaction.showModal(modal);
  }
});
```

## Recibir Envíos de Modal

```js
client.on(Events.InteractionCreate, (interaction) => {
  if (!interaction.isModalSubmit()) return;
  console.log(interaction.customId); // 'miModal'
});
```

## Responder a Envíos

Métodos disponibles: `reply()`, `editReply()`, `deferReply()`, `fetchReply()`, `deleteReply()`, `followUp()`, `update()`, `deferUpdate()`.

```js
client.on(Events.InteractionCreate, async (interaction) => {
  if (!interaction.isModalSubmit()) return;
  if (interaction.customId === 'miModal') {
    await interaction.reply({ content: '¡Recibido correctamente!' });
  }
});
```

## Extraer Datos

```js
client.on(Events.InteractionCreate, async (interaction) => {
  if (!interaction.isModalSubmit()) return;
  if (interaction.customId === 'miModal') {
    const nombre = interaction.fields.getTextInputValue('nombre');
    const starter = interaction.fields.getStringSelectValues('starter');
    const archivos = interaction.fields.getUploadedFiles('picture');

    console.log({ nombre, starter, archivos });
  }
});
```

### ⚠️ Detalles importantes sobre `getUploadedFiles()`

`interaction.fields.getUploadedFiles('campo')` devuelve un **`Collection<string, Attachment>`** de discord.js, **NO** un Array nativo de JavaScript.

```js
const files = interaction.fields.getUploadedFiles('picture');

// ✅ Válido — Collection tiene .size
if (files?.size > 0) {
  console.log(`Archivos subidos: ${files.size}`);
  console.log(files.first().url);       // URL de la CDN de Discord
  console.log(files.map(a => a.url));   // Todas las URLs
}

// ❌ NO funciona — Collection no tiene .length
if (files?.length) { /* undefined */ }
```

### 🖼️ Usar archivos de modal directamente en MediaGallery

La URL que devuelve `files.first().url` es una URL directa de la CDN de Discord. Puedes usarla directamente en `MediaGalleryItemBuilder.setURL()` — **no necesitas** descargar con `fetch` ni re-subir con `AttachmentBuilder`:

```js
const imgFiles = interaction.fields.getUploadedFiles('imagen');
if (imgFiles?.size) {
  container.addMediaGalleryComponents(
    new MediaGalleryBuilder().addItems(
      new MediaGalleryItemBuilder()
        .setURL(imgFiles.first().url)  // ✅ URL directa de la CDN
        .setDescription('Imagen subida')
    )
  );
}
```

### 🧩 Ejemplo completo: Modal con FileUploadBuilder → MediaGallery

Flujo completo: el usuario abre un modal, sube una imagen, y al enviar se muestra en una galería CV2.

```js
const { ModalBuilder, LabelBuilder, FileUploadBuilder, TextInputBuilder, TextInputStyle,
  ContainerBuilder, TextDisplayBuilder, MediaGalleryBuilder, MediaGalleryItemBuilder,
  MessageFlags } = require('discord.js');

// ── Mostrar el modal ──
client.on(Events.InteractionCreate, async (interaction) => {
  if (!interaction.isChatInputCommand()) return;
  if (interaction.commandName === 'subirimagen') {
    const modal = new ModalBuilder().setCustomId('img_modal').setTitle('Subir imagen');

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
  }
});

// ── Recibir el modal y mostrar en MediaGallery ──
client.on(Events.InteractionCreate, async (interaction) => {
  if (!interaction.isModalSubmit()) return;
  if (interaction.customId === 'img_modal') {
    await interaction.deferReply();

    const titulo = interaction.fields.getTextInputValue('titulo');
    const archivos = interaction.fields.getUploadedFiles('imagen');

    const container = new ContainerBuilder()
      .addTextDisplayComponents(
        new TextDisplayBuilder().setContent(`### ${titulo}`)
      );

    if (archivos?.size) {
      container.addMediaGalleryComponents(
        new MediaGalleryBuilder().addItems(
          new MediaGalleryItemBuilder()
            .setURL(archivos.first().url)
            .setDescription(titulo)
        )
      );
    }

    await interaction.editReply({
      components: [container],
      flags: MessageFlags.IsComponentsV2,
    });
  }
});
```

Los campos de texto vacíos devuelven `""`. Los selects sin selección devuelven `[]`.
