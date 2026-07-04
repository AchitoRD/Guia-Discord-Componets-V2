# 14. :bulb: Consejos y Errores Comunes

> :warning: **Requisito:** discord.js **>= 14.23.0** — las APIs de Components V2, `LabelBuilder`, `FileUploadBuilder`, `addLabelComponents`, etc. no existen en versiones anteriores.

Errores frecuentes, límites y cosas que debes saber antes de publicar.

---

## Siempre Pasa la Bandera

Cada mensaje CV2 **debe** incluir `MessageFlags.IsComponentsV2`. Sin ella, los componentes de visualización no se renderizarán.

```js
await channel.send({
  components: [container],
  flags: MessageFlags.IsComponentsV2, // ← obligatorio cada vez
});
```

## Los Componentes Antiguos Siguen Funcionando

CV2 es **optativo por mensaje** — no tienes que migrar todo tu bot. Los mensajes antiguos con `ActionRow` siguen funcionando junto a CV2 en el mismo bot.

## Sin Embeds, Content, Stickers ni Polls

Al enviar un mensaje CV2, **no puedes** incluir:
- `content`
- `embeds`
- `stickers`
- `poll`

```js
// Incorrecto
await channel.send({ content: 'Hola', components: [container], flags: MessageFlags.IsComponentsV2 });

// Correcto — CV2 maneja todo
await channel.send({ components: [container], flags: MessageFlags.IsComponentsV2 });
```

> **Consejo de edición:** Para convertir un mensaje no-CV2 a CV2, establece `content: null` y `embeds: []` en la llamada de edición.

## La Bandera No se Puede Eliminar

Una vez que un mensaje se envía con `IsComponentsV2`, la bandera **no puede eliminarse** de ese mensaje editándolo. Solo puedes editar el contenido de los componentes.

## Los Contenedores No se Pueden Anidar

No puedes poner un `ContainerBuilder` dentro de otro `ContainerBuilder`:

```
components: [
  ContainerBuilder   ✅
  ContainerBuilder   ✅
    └── ContainerBuilder  ❌ no permitido
]
```

## attachment:// Debe Coincidir Exactamente

```js
const file = new AttachmentBuilder('./report.txt').setName('report.txt');
new FileBuilder().setURL('attachment://report.txt')  // ✅

new FileBuilder().setURL('attachment://Report.txt')  // ❌ sensible a mayúsculas
```

Siempre llama a `.setName()` en `AttachmentBuilder` explícitamente.

## Las Menciones Harán Ping

Las menciones dentro de `TextDisplayBuilder` **notificarán** a esos usuarios/roles:

```js
await channel.send({
  components: [new TextDisplayBuilder().setContent(`Hola <@${userId}>`)],
  flags: MessageFlags.IsComponentsV2,
  allowedMentions: { users: [] }, // suprimir pings
});
```

## SectionBuilder Requiere Accesorio Obligatorio

`SectionBuilder` **siempre necesita** un accesorio (`.setThumbnailAccessory()` o `.setButtonAccessory()`). Sin él, lanza:

```
CombinedError: Expected ButtonBuilder | ThumbnailBuilder, received undefined
```

Si no necesitas imagen ni botón, usa `TextDisplayBuilder` directamente dentro del `ContainerBuilder`.

## Sections: 1–3 TextDisplays

Un `SectionBuilder` acepta entre **1 y 3** elementos `TextDisplayBuilder`. Más de 3 causará un error.

## Límites de Componentes

| Límite | Valor |
|--------|-------|
| Componentes totales por mensaje | **40 (incluyendo anidados)** |
| TextDisplays por sección | 1–3 |
| Botones por ActionRow | 5 |
| Opciones de select | 25 |
| Elementos en MediaGallery | 10 |
| Texto total en todos los TextDisplays | 4000 caracteres |

El límite de **40 componentes** es el más importante — cada `ContainerBuilder`, `TextDisplayBuilder`, `SeparatorBuilder`, `SectionBuilder`, `ActionRowBuilder`, `ButtonBuilder`, etc. cuenta, incluyendo los anidados.

## TextInputBuilder NO lleva `.setLabel()` dentro de LabelBuilder

Cuando un `TextInputBuilder` está envuelto en un `LabelBuilder`, **no debe** tener `.setLabel()`. La etiqueta va únicamente en el `LabelBuilder`:

```js
// :x: INCORRECTO — lanza TEXT_INPUT_COMPONENT_LABEL_IN_LABEL_COMPONENT
new TextInputBuilder().setLabel('Título').setCustomId('titulo')

// :white_check_mark: CORRECTO — la label va en LabelBuilder
new LabelBuilder()
  .setLabel('Título')
  .setTextInputComponent(
    new TextInputBuilder().setCustomId('titulo')
  )
```

## getUploadedFiles() devuelve Collection, no Array

`interaction.fields.getUploadedFiles('campo')` devuelve un `Collection<string, Attachment>` de discord.js, **no** un Array.

```js
const files = interaction.fields.getUploadedFiles('imagen');

// :white_check_mark: Válido
if (files?.size > 0) { /* hay archivos */ }
files.first().url        // URL del primer archivo
files.map(a => a.url)    // URLs de todos

// :x: NO funciona
if (files?.length) { /* undefined — Collection no tiene .length */ }
```

## Los Modals Requieren Respuesta Inmediata

Mostrar un modal debe ser la **primera respuesta** a una interacción. No puedes hacer defer ni enviar un mensaje antes.

## Botones Link vs Botones Normales

- Los botones con `ButtonStyle.Link` requieren `.setURL()` y **no** necesitan `custom_id`
- Los botones Link **no** disparan interacciones — abren la URL directamente
- Todos los demás estilos requieren `custom_id`

## 3 Segundos para Responder

Toda interacción (botón, select, modal) debe recibir una respuesta dentro de **3 segundos** o Discord la considerará fallida. Usa `deferReply()` o `deferUpdate()` si necesitas más tiempo.

---

> :book: **Guía original** por **itsfizys** — :link: [discordjs-components-v2-guide](https://github.com/itsfizys/discordjs-components-v2-guide)  
> :speech_balloon: **Soporte:** [discord.gg/aerox](https://discord.gg/aerox)
