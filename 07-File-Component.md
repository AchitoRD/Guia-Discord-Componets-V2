# 7. File Component

`FileBuilder` te permite mostrar un archivo adjunto **en línea** dentro de un diseño CV2 — transcripciones, logs, exportaciones, lo que sea.

---

## Uso Básico

Necesitas dos cosas:
1. Un `AttachmentBuilder` (el archivo real a subir)
2. Un `FileBuilder` que lo referencie mediante `attachment://filename`

```js
const { FileBuilder, AttachmentBuilder, ContainerBuilder, TextDisplayBuilder, MessageFlags } = require('discord.js');

const file = new AttachmentBuilder('./transcript.txt').setName('transcript.txt');
const fileComponent = new FileBuilder().setURL('attachment://transcript.txt');

await channel.send({
  components: [fileComponent],
  files: [file],
  flags: MessageFlags.IsComponentsV2,
});
```

## Dentro de un Container

```js
const container = new ContainerBuilder()
  .addTextDisplayComponents(
    new TextDisplayBuilder().setContent('### Transcripción del Ticket')
  )
  .addSeparatorComponents(
    new SeparatorBuilder().setDivider(true).setSpacing(SeparatorSpacingSize.Small)
  )
  .addTextDisplayComponents(
    new TextDisplayBuilder().setContent(`Ticket cerrado por <@${user.id}>`)
  )
  .addFileComponents(
    new FileBuilder().setURL('attachment://ticket-0042-transcript.txt')
  );

const attachment = new AttachmentBuilder('./ticket-0042-transcript.txt');

await channel.send({
  components: [container],
  files: [attachment],
  flags: MessageFlags.IsComponentsV2,
});
```

## Desde un Buffer

```js
const { Readable } = require('stream');

const content = 'Hola, este es el contenido del archivo.';
const stream = Readable.from([content]);
const file = new AttachmentBuilder(stream).setName('output.txt');
const fileComponent = new FileBuilder().setURL('attachment://output.txt');
```

## Enviar a los MDs de un Usuario

```js
await user.send({
  components: [fileComponent],
  files: [file],
  flags: MessageFlags.IsComponentsV2,
});
```

## Archivo Spoiler

```js
new FileBuilder().setURL('attachment://secret.txt').setSpoiler(true)
```

## Notas

- El nombre de archivo en `attachment://` debe **coincidir exactamente** con el nombre establecido en `AttachmentBuilder` — distingue mayúsculas/minúsculas
- Solo **un** `FileBuilder` por mensaje (limitación de Discord)
- Siempre llama a `.setName()` en el `AttachmentBuilder` para que el nombre de archivo sea explícito
