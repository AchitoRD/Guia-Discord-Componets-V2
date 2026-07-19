# 14 - Consejos y Errores Comunes

## Version correcta

Requiere discord.js >= 14.23.0. Las APIs CV2 no existen en versiones anteriores.

```bash
npm install discord.js@latest
```

## Siempre pasar la bandera

Cada mensaje CV2 debe incluir `MessageFlags.IsComponentsV2`. Sin ella, los componentes no se renderizan.

```typescript
await channel.send({
    components: [container],
    flags: MessageFlags.IsComponentsV2,
});
```

## Sin embeds, content, stickers ni polls

```typescript
// Incorrecto
await channel.send({ content: "Hola", components: [container], flags: MessageFlags.IsComponentsV2 });

// Correcto
await channel.send({ components: [container], flags: MessageFlags.IsComponentsV2 });
```

## La bandera no se puede eliminar

Una vez enviado con `IsComponentsV2`, no puedes quitarla editando el mensaje.

## Containers no se pueden anidar

```
components: [
  ContainerBuilder   OK
  ContainerBuilder   OK
    +-- ContainerBuilder  NO permitido
]
```

## attachment:// debe coincidir exactamente

```typescript
const file = new AttachmentBuilder("./report.txt").setName("report.txt");
new FileBuilder().setURL("attachment://report.txt");  // OK
new FileBuilder().setURL("attachment://Report.txt");  // ERROR - sensible a mayusculas
```

## Menciones hacen ping

```typescript
await channel.send({
    components: [new TextDisplayBuilder().setContent(`Hola <@${userId}>`)],
    flags: MessageFlags.IsComponentsV2,
    allowedMentions: { users: [] },
});
```

## SectionBuilder requiere accesorio

```typescript
// ERROR - CombinedError: Expected ButtonBuilder | ThumbnailBuilder
new SectionBuilder().addTextDisplayComponents(new TextDisplayBuilder().setContent("texto"));

// OK
new SectionBuilder()
    .addTextDisplayComponents(new TextDisplayBuilder().setContent("texto"))
    .setThumbnailAccessory(new ThumbnailBuilder().setURL(url));
```

## TextInputBuilder sin .setLabel() dentro de LabelBuilder

```typescript
// ERROR - TEXT_INPUT_COMPONENT_LABEL_IN_LABEL_COMPONENT
new TextInputBuilder().setLabel("Titulo").setCustomId("t");

// OK
new LabelBuilder()
    .setLabel("Titulo")
    .setTextInputComponent(new TextInputBuilder().setCustomId("t"));
```

## getUploadedFiles() es Collection, no Array

```typescript
const files = interaction.fields.getUploadedFiles("campo");

// OK
if (files?.size > 0) { }
files.first()!.url;

// NO funciona
// files?.length // undefined
```

## Limite de componentes

| Componente | Limite |
|------------|--------|
| Componentes totales por mensaje | **40 (incluyendo anidados)** |
| TextDisplays por seccion | 1-3 |
| Botones por ActionRow | 5 |
| Opciones de select | 25 |
| Items en MediaGallery | 10 |
| Texto total TextDisplay | 4000 caracteres |

## 3 segundos para responder

Toda interaccion requiere respuesta en 3 segundos. Usa `deferReply()` o `deferUpdate()` si necesitas mas tiempo.

## Modals: primera respuesta obligatoria

Mostrar un modal debe ser la primera respuesta. No puedes hacer defer ni enviar otro mensaje antes.
