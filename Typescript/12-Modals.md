# 12 - Modals

Los modals son ventanas emergentes para solicitar datos al usuario.

## Crear y mostrar un modal

```typescript
import { ModalBuilder, LabelBuilder, TextInputBuilder, TextInputStyle, ActionRowBuilder, type ModalActionRowComponentBuilder } from 'discord.js';

const modal = new ModalBuilder()
    .setCustomId("miModal")
    .setTitle("Formulario");

const nombreInput = new TextInputBuilder()
    .setCustomId("nombre")
    .setStyle(TextInputStyle.Short)
    .setMaxLength(50);

const nombreLabel = new LabelBuilder()
    .setLabel("Nombre completo")
    .setTextInputComponent(nombreInput);

modal.addLabelComponents(nombreLabel);

await interaction.showModal(modal);
```

IMPORTANTE: `TextInputBuilder` dentro de `LabelBuilder` NO debe tener `.setLabel()`. La etiqueta va solo en el `LabelBuilder`:

```typescript
// INCORRECTO - lanza TEXT_INPUT_COMPONENT_LABEL_IN_LABEL_COMPONENT
new TextInputBuilder().setLabel("Titulo").setCustomId("titulo");

// CORRECTO
new LabelBuilder()
    .setLabel("Titulo")
    .setTextInputComponent(
        new TextInputBuilder().setCustomId("titulo"),
    );
```

## Multiples campos

```typescript
const modal = new ModalBuilder()
    .setCustomId("ticketModal")
    .setTitle("Abrir Ticket");

const tituloInput = new TextInputBuilder()
    .setCustomId("titulo")
    .setStyle(TextInputStyle.Short)
    .setMaxLength(100);

const descInput = new TextInputBuilder()
    .setCustomId("descripcion")
    .setStyle(TextInputStyle.Paragraph)
    .setMaxLength(1000);

const tituloLabel = new LabelBuilder()
    .setLabel("Titulo del problema")
    .setTextInputComponent(tituloInput);

const descLabel = new LabelBuilder()
    .setLabel("Descripcion detallada")
    .setTextInputComponent(descInput);

modal.addLabelComponents(tituloLabel, descLabel);

await interaction.showModal(modal);
```

## Select en modal

```typescript
const select = new StringSelectMenuBuilder()
    .setCustomId("categoria")
    .setPlaceholder("Elige una categoria")
    .setRequired(true)
    .addOptions(
        new StringSelectMenuOptionBuilder().setLabel("Soporte").setValue("soporte"),
        new StringSelectMenuOptionBuilder().setLabel("Facturacion").setValue("billing"),
    );

const selectLabel = new LabelBuilder()
    .setLabel("Categoria")
    .setStringSelectMenuComponent(select);

modal.addLabelComponents(selectLabel);
```

## FileUpload en modal

```typescript
import { FileUploadBuilder } from 'discord.js';

const upload = new FileUploadBuilder()
    .setCustomId("imagen")
    .setMaxValues(1);

const uploadLabel = new LabelBuilder()
    .setLabel("Sube una imagen")
    .setFileUploadComponent(upload);

modal.addLabelComponents(uploadLabel);
```

## Recibir el modal

```typescript
client.on(Events.InteractionCreate, async interaction => {
    if (!interaction.isModalSubmit()) return;

    if (interaction.customId === "miModal") {
        await interaction.reply({ content: "Recibido!" });

        const nombre = interaction.fields.getTextInputValue("nombre");
        console.log(nombre);
    }
});
```

## Extraer datos - getUploadedFiles() es Collection

`interaction.fields.getUploadedFiles("campo")` devuelve un `Collection<string, Attachment>`, NO un Array.

```typescript
const files = interaction.fields.getUploadedFiles("imagen");

// VALIDO
if (files?.size > 0) {
    console.log(`Archivos: ${files.size}`);
    console.log(files.first()!.url);      // URL CDN
    console.log(files.map(a => a.url));   // todas las URLs
}

// NO funciona - Collection no tiene .length
// files?.length // undefined
```

## Usar archivos de modal en MediaGallery

La URL de `files.first().url` es una URL directa de la CDN de Discord. Puedes usarla directamente en `MediaGalleryItemBuilder.setURL()`:

```typescript
const imgFiles = interaction.fields.getUploadedFiles("imagen");

if (imgFiles?.size) {
    container.addMediaGalleryComponents(
        new MediaGalleryBuilder().addItems(
            new MediaGalleryItemBuilder()
                .setURL(imgFiles.first()!.url)
                .setDescription("Imagen subida"),
        ),
    );
}
```

## Ejemplo completo: modal + MediaGallery

```typescript
// Mostrar modal
const modal = new ModalBuilder().setCustomId("img_modal").setTitle("Subir imagen");

const tituloInput = new TextInputBuilder()
    .setCustomId("titulo").setStyle(TextInputStyle.Short).setMaxLength(100);

const tituloLabel = new LabelBuilder()
    .setLabel("Titulo de la imagen")
    .setTextInputComponent(tituloInput);

const fileUpload = new FileUploadBuilder().setCustomId("imagen").setMaxValues(1);

const fileLabel = new LabelBuilder()
    .setLabel("Selecciona una imagen")
    .setFileUploadComponent(fileUpload);

modal.addLabelComponents(tituloLabel, fileLabel);
await interaction.showModal(modal);

// Recibir modal
client.on(Events.InteractionCreate, async interaction => {
    if (!interaction.isModalSubmit() || interaction.customId !== "img_modal") return;

    await interaction.deferReply();

    const titulo = interaction.fields.getTextInputValue("titulo");
    const archivos = interaction.fields.getUploadedFiles("imagen");

    const container = new ContainerBuilder()
        .addTextDisplayComponents(
            new TextDisplayBuilder().setContent(`### ${titulo}`),
        );

    if (archivos?.size) {
        container.addMediaGalleryComponents(
            new MediaGalleryBuilder().addItems(
                new MediaGalleryItemBuilder()
                    .setURL(archivos.first()!.url)
                    .setDescription(titulo),
            ),
        );
    }

    await interaction.editReply({
        components: [container],
        flags: MessageFlags.IsComponentsV2,
    });
});
```

## Notas

- Maximo **5** componentes de primer nivel por modal
- Mostrar modal debe ser la **primera respuesta** - no puedes hacer defer
- TextInput vacio devuelve `""`
- Select sin seleccion devuelve `[]`
