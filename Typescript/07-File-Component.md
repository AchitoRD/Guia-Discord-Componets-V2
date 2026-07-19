# 07 - File Component

`FileBuilder` muestra un archivo adjunto **en linea** dentro de un layout CV2.

## Uso basico

```typescript
import { FileBuilder, AttachmentBuilder, MessageFlags } from 'discord.js';

const file = new AttachmentBuilder("./transcript.txt").setName("transcript.txt");
const fileComponent = new FileBuilder().setURL("attachment://transcript.txt");

await channel.send({
    components: [fileComponent],
    files: [file],
    flags: MessageFlags.IsComponentsV2,
});
```

## Dentro de un container

```typescript
import { ContainerBuilder, TextDisplayBuilder, SeparatorBuilder, SeparatorSpacingSize, FileBuilder, AttachmentBuilder, MessageFlags } from 'discord.js';

const container = new ContainerBuilder()
    .addTextDisplayComponents(
        new TextDisplayBuilder().setContent("### Transcripcion"),
    )
    .addSeparatorComponents(
        new SeparatorBuilder().setDivider(true).setSpacing(SeparatorSpacingSize.Small),
    )
    .addTextDisplayComponents(
        new TextDisplayBuilder().setContent(`Ticket cerrado por <@${user.id}>`),
    )
    .addFileComponents(
        new FileBuilder().setURL("attachment://ticket-0042-transcript.txt"),
    );

const attachment = new AttachmentBuilder("./ticket-0042-transcript.txt");

await channel.send({
    components: [container],
    files: [attachment],
    flags: MessageFlags.IsComponentsV2,
});
```

## Desde un buffer

```typescript
import { Readable } from 'stream';
import { AttachmentBuilder, FileBuilder } from 'discord.js';

const content = "Contenido del archivo.";
const stream = Readable.from([content]);
const file = new AttachmentBuilder(stream).setName("output.txt");
const fileComponent = new FileBuilder().setURL("attachment://output.txt");
```

## Enviar a MDs

```typescript
await user.send({
    components: [fileComponent],
    files: [file],
    flags: MessageFlags.IsComponentsV2,
});
```

## Archivo spoiler

```typescript
new FileBuilder().setURL("attachment://secret.txt").setSpoiler(true);
```

## Notas

- El nombre en `attachment://` debe coincidir **exactamente** con `AttachmentBuilder.setName()` (sensible a mayusculas)
- Solo **un** `FileBuilder` por mensaje (limitacion de Discord)
- Siempre usa `.setName()` en el `AttachmentBuilder`
