# Guia-Discord-Componets-V2
📘 Guía completa de Discord Components V2 en español, con ejemplos prácticos, documentación actualizada y contenido complementario para facilitar el desarrollo de bots con discord.js.

# Discord.js Components V2 — Guía para Principiantes
# Discord.js Components V2 — Guía para Principiantes

> Guía por **itsfizys** — Traducida y ampliada • Servidor de Soporte: [discord.gg/aerox](https://discord.gg/aerox)

Components V2 es el nuevo sistema de diseño de mensajes de Discord que te da **control total** sobre el aspecto de los mensajes de tu bot. Es más potente y flexible que el sistema tradicional de `embed`.

---

## Novedades en Components V2

- Diseños totalmente personalizables
- Texto enriquecido con soporte completo de Markdown
- UI limpia con secciones, imágenes y espaciado
- Botones y menús desplegables funcionan directamente en el diseño
- `content`, `embeds`, `stickers` y `polls` **no pueden** usarse junto con componentes CV2
- Debes pasar la bandera `MessageFlags.IsComponentsV2` en cada mensaje CV2

---

## Bloques de Construcción Básicos

| Builder | Qué Hace |
|---------|----------|
| `ContainerBuilder` | Bloque de diseño exterior con color de acento opcional |
| `TextDisplayBuilder` | Renderiza texto con soporte completo de Markdown |
| `SectionBuilder` | Agrupa texto lado a lado con una miniatura o botón |
| `SeparatorBuilder` | Añade una línea divisoria o espacio entre componentes |
| `MediaGalleryBuilder` | Muestra imágenes/videos en una cuadrícula |
| `FileBuilder` | Adjunta y muestra archivos en línea |
| `ActionRowBuilder` | Añade botones o menús desplegables dentro de un diseño |
| `ThumbnailBuilder` | Accesorio de imagen pequeña dentro de una Section |

---

## Tabla de Contenidos

| # | Guía | Descripción |
|---|------|-------------|
| 1 | [Estructura del Mensaje](#1-estructura-del-mensaje) | Cómo se estructuran los mensajes CV2 |
| 2 | [Container](#2-container) | Agrupación de componentes con color de acento opcional |
| 3 | [TextDisplay](#3-textdisplay) | Renderizado de texto Markdown |
| 4 | [Separator](#4-separator) | Divisores visuales y espaciado |
| 5 | [Section y Thumbnail](#5-section-y-thumbnail) | Texto lado a lado + imagen o botón |
| 6 | [MediaGallery](#6-mediagallery) | Cuadrículas de imágenes y videos |
| 7 | [File Component](#7-file-component) | Archivos adjuntos en línea |
| 8 | [ActionRow](#8-actionrow) | Botones y selects dentro de CV2 |
| 9 | [Botones](#9-botones) | Componentes interactivos de botón |
| 10 | [Menús Desplegables (Select Menus)](#10-menús-desplegables-select-menus) | Menús de selección de opciones |
| 11 | [Manejo de Interacciones](#11-manejo-de-interacciones) | Cómo responder a clics y selecciones |
| 12 | [Modals (Ventanas Emergentes)](#12-modals-ventanas-emergentes) | Formularios emergentes para entrada de datos |
| 13 | [Ejemplos del Mundo Real](#13-ejemplos-del-mundo-real) | Patrones completos de un bot en producción |
| 14 | [Consejos y Errores Comunes](#14-consejos-y-errores-comunes) | Límites, errores y mejores prácticas |

---

## 1. Estructura del Mensaje

En Discord.js no existe una clase `LayoutView`. Un mensaje CV2 es simplemente una llamada normal a `send()` o `reply()` con dos cosas clave:

1. Un array `components` que contiene tus builders
2. La bandera `MessageFlags.IsComponentsV2`

### Estructura Básica

```js
const { MessageFlags } = require('discord.js');

await channel.send({
  components: [/* tus componentes aquí */],
  flags: MessageFlags.IsComponentsV2,
});
```

### Responder a una Interacción

```js
await interaction.reply({
  components: [container],
  flags: MessageFlags.IsComponentsV2,
});
```

### Respuesta Efímera

```js
await interaction.reply({
  components: [container],
  flags: MessageFlags.IsComponentsV2 | MessageFlags.Ephemeral,
});
```

### Seguimiento Después de un Defer

```js
await interaction.deferReply();

// ... trabajo asíncrono ...

await interaction.editReply({
  components: [container],
  flags: MessageFlags.IsComponentsV2,
});
```

> No necesitas la bandera al hacer defer — solo cuando envías el contenido real del componente.

### Múltiples Componentes de Primer Nivel

Puedes pasar múltiples builders en el array — se apilan verticalmente:

```js
await channel.send({
  components: [textDisplay, separator, container],
  flags: MessageFlags.IsComponentsV2,
});
```

### Cómo Encaja Todo

```
components: [
  ContainerBuilder
  ├── TextDisplayBuilder
  ├── SeparatorBuilder
  ├── SectionBuilder
  │   ├── TextDisplayBuilder
  │   └── ThumbnailBuilder (accesorio)
  ├── MediaGalleryBuilder
  └── ActionRowBuilder
      └── ButtonBuilder
]
```

### `id` del Componente vs `custom_id`

Cada componente puede tener un campo `id` numérico opcional — **no** es lo mismo que `custom_id`.

```js
new TextDisplayBuilder().setContent('Count: 0').setId(1001)
```

- `id` — un número que estableces para encontrar un componente después en respuestas de interacción
- `custom_id` — un string en botones/selects que activa eventos de interacción

### Notas

- La bandera `IsComponentsV2` **no puede eliminarse** de un mensaje una vez enviado
- Una vez establecida, `content`, `embeds`, `stickers` y `polls` están bloqueados en ese mensaje
- Los mensajes con `components: [actionRow]` al estilo antiguo siguen funcionando — CV2 es optativo por mensaje
- Límite total de componentes: **40** (incluyendo anidados)
- Límite total de texto: **4000 caracteres** en todo el contenido de `TextDisplayBuilder`

---

## 2. Container

`ContainerBuilder` es el bloque de construcción principal de un diseño CV2. Agrupa componentes y opcionalmente añade una barra de acento de color en el lado izquierdo — similar a la franja de color de un embed de Discord.

### Uso Básico

```js
const { ContainerBuilder, TextDisplayBuilder, MessageFlags } = require('discord.js');

const container = new ContainerBuilder()
  .addTextDisplayComponents(
    new TextDisplayBuilder().setContent('¡Hola!')
  );

await channel.send({
  components: [container],
  flags: MessageFlags.IsComponentsV2,
});
```

### Con Color de Acento

```js
const container = new ContainerBuilder()
  .setAccentColor(0x5865F2); // Discord blurple
```

Pasa cualquier color hex como número:

```js
new ContainerBuilder().setAccentColor(0x57F287) // verde
new ContainerBuilder().setAccentColor(0xED4245) // rojo
new ContainerBuilder().setAccentColor(0xFF8C00) // naranja
```

### Sin Color (Predeterminado)

Simplemente no llames a `.setAccentColor()` — el contenedor se renderiza como un recuadro simple sin barra de acento.

### Contenedor Spoiler

Puedes desenfocar todo el contenedor hasta que el usuario haga clic:

```js
const container = new ContainerBuilder()
  .setSpoiler(true)
  .addTextDisplayComponents(
    new TextDisplayBuilder().setContent('¡Contenido oculto!')
  );
```

### Añadir Múltiples Componentes

```js
const { ContainerBuilder, TextDisplayBuilder, SeparatorBuilder, SeparatorSpacingSize } = require('discord.js');

const container = new ContainerBuilder()
  .setAccentColor(0x5865F2)
  .addTextDisplayComponents(
    new TextDisplayBuilder().setContent('### Título')
  )
  .addSeparatorComponents(
    new SeparatorBuilder().setDivider(true).setSpacing(SeparatorSpacingSize.Small)
  )
  .addTextDisplayComponents(
    new TextDisplayBuilder().setContent('Algo de texto aquí.')
  );
```

### Notas

- Un mensaje puede contener **múltiples** contenedores en el primer nivel
- Los contenedores pueden contener: `TextDisplayBuilder`, `SeparatorBuilder`, `SectionBuilder`, `MediaGalleryBuilder`, `FileBuilder`, `ActionRowBuilder`
- Los contenedores **no pueden** anidarse dentro de otros contenedores

---

## 3. TextDisplay

`TextDisplayBuilder` renderiza texto Markdown dentro de un diseño CV2. Es la forma principal de mostrar cualquier contenido escrito — títulos, descripciones, información tipo campo, etc. Reemplaza directamente el antiguo campo `content` y las descripciones de embeds.

### Uso Básico

```js
const { TextDisplayBuilder, MessageFlags } = require('discord.js');

const text = new TextDisplayBuilder()
  .setContent('¡Hola, mundo!');

await channel.send({
  components: [text],
  flags: MessageFlags.IsComponentsV2,
});
```

### Markdown Totalmente Soportado

```js
new TextDisplayBuilder().setContent('### Ticket Abierto')
new TextDisplayBuilder().setContent('**Estado:** Abierto')
new TextDisplayBuilder().setContent('>>> Esto es un blockquote')
new TextDisplayBuilder().setContent('`código inline` también funciona')
new TextDisplayBuilder().setContent('-# Texto pequeño al final')
```

### Texto Multilínea

```js
new TextDisplayBuilder().setContent(
  '**Usuario:** Juan\n' +
  '**Categoría:** Soporte\n' +
  '**Creado:** <t:1700000000:R>'
)
```

### Marcas de Tiempo de Discord

```js
const ts = Math.floor(Date.now() / 1000);

new TextDisplayBuilder().setContent(`Abierto <t:${ts}:R>`)  // "hace 5 minutos"
new TextDisplayBuilder().setContent(`Abierto <t:${ts}:F>`)  // "lunes, 1 de enero de 2024 12:00"
```

### Menciones de Usuario y Canal

Las menciones dentro de `TextDisplayBuilder` **notificarán a usuarios y roles** — usa `allowedMentions` para controlar esto:

```js
await channel.send({
  components: [
    new TextDisplayBuilder().setContent(`Creado por <@${user.id}>`)
  ],
  flags: MessageFlags.IsComponentsV2,
  allowedMentions: { users: [] }, // suprimir el ping
});
```

### Establecer un ID de Componente

```js
const DISPLAY_ID = 1001;

new TextDisplayBuilder()
  .setContent('Count: 0')
  .setId(DISPLAY_ID)
```

Usa `.setId()` para poder encontrar y actualizar este componente después mediante respuestas de interacción.

### Notas

- Puedes tener tantos `TextDisplayBuilder` como necesites
- No hay división título/descripción como en los embeds — usa encabezados Markdown (`#`, `##`, `###`)
- El texto total en **todos** los componentes `TextDisplayBuilder` en un mensaje no puede exceder **4000 caracteres**
- Los emojis (unicode y personalizados de Discord) se renderizan bien

---

## 4. Separator

`SeparatorBuilder` añade una línea divisoria horizontal o espacio entre componentes. Ideal para separar un título del contenido del cuerpo o para dividir secciones visuales.

### Uso Básico

```js
const { SeparatorBuilder, SeparatorSpacingSize } = require('discord.js');

const separator = new SeparatorBuilder()
  .setDivider(true)
  .setSpacing(SeparatorSpacingSize.Small);
```

### Variantes de Espaciado

```js
new SeparatorBuilder().setSpacing(SeparatorSpacingSize.Small)
new SeparatorBuilder().setSpacing(SeparatorSpacingSize.Large)
```

`Small` añade un espacio pequeño. `Large` añade más espacio de respiro.

### Separador Invisible (Solo Espaciador)

Si quieres espacio sin la línea visible:

```js
new SeparatorBuilder().setDivider(false).setSpacing(SeparatorSpacingSize.Small)
```

Esto añade espacio en blanco entre componentes sin dibujar una línea.

### Dentro de un Container

```js
const { ContainerBuilder, TextDisplayBuilder, SeparatorBuilder, SeparatorSpacingSize } = require('discord.js');

const container = new ContainerBuilder()
  .addTextDisplayComponents(
    new TextDisplayBuilder().setContent('### Ticket Cerrado')
  )
  .addSeparatorComponents(
    new SeparatorBuilder().setDivider(true).setSpacing(SeparatorSpacingSize.Small)
  )
  .addTextDisplayComponents(
    new TextDisplayBuilder().setContent(`Cerrado por <@${user.id}>`)
  )
  .addSeparatorComponents(
    new SeparatorBuilder().setDivider(true).setSpacing(SeparatorSpacingSize.Small)
  )
  .addTextDisplayComponents(
    new TextDisplayBuilder().setContent('Se ha enviado una transcripción a tus MDs.')
  );
```

### Notas

- Los separadores son puramente visuales — no contienen datos
- Puedes usar tantos separadores como necesites dentro de un contenedor
- Por defecto `setDivider(true)` muestra una línea visible; `false` es solo espacio

---

## 5. Section y Thumbnail

`SectionBuilder` coloca texto **lado a lado** con un accesorio — ya sea un `ThumbnailBuilder` (imagen) o un `ButtonBuilder`. Útil para tarjetas de perfil, paneles de información de usuario, o cualquier combinación de texto + imagen.

### Con una Thumbnail (Imagen)

```js
const { SectionBuilder, ThumbnailBuilder, TextDisplayBuilder, MessageFlags } = require('discord.js');

const section = new SectionBuilder()
  .addTextDisplayComponents(
    new TextDisplayBuilder().setContent('**Juan Pérez**'),
    new TextDisplayBuilder().setContent('Ticket de soporte abierto.')
  )
  .setThumbnailAccessory(
    new ThumbnailBuilder().setURL('https://example.com/avatar.png')
  );

await channel.send({
  components: [section],
  flags: MessageFlags.IsComponentsV2,
});
```

### Con el Avatar de un Usuario

```js
const section = new SectionBuilder()
  .addTextDisplayComponents(
    new TextDisplayBuilder().setContent(`**${interaction.user.displayName}**`),
    new TextDisplayBuilder().setContent('Categoría: General')
  )
  .setThumbnailAccessory(
    new ThumbnailBuilder()
      .setURL(interaction.user.displayAvatarURL())
      .setDescription('Avatar de usuario') // texto alternativo
  );
```

### Con el Icono del Servidor

```js
const iconUrl = interaction.guild.iconURL();

const section = new SectionBuilder()
  .addTextDisplayComponents(
    new TextDisplayBuilder().setContent(`**__${title}__**\n\n${description}`)
  )
  .setThumbnailAccessory(
    new ThumbnailBuilder().setURL(iconUrl)
  );
```

### Cómo se Renderiza

```
┌─────────────────────────────────────────┐
│  Contenido de texto aquí          [img] │
│  Más texto en el lado izquierdo         │
└─────────────────────────────────────────┘
```

La miniatura aparece a la **derecha**, el texto a la **izquierda**.

### Botón como Accesorio

Un `ButtonBuilder` también puede ser el accesorio de la sección — colocado en el lado derecho junto al texto:

```js
const { SectionBuilder, TextDisplayBuilder, ButtonBuilder, ButtonStyle } = require('discord.js');

const section = new SectionBuilder()
  .addTextDisplayComponents(
    new TextDisplayBuilder().setContent('Count: 0').setId(1001)
  )
  .setButtonAccessory(
    new ButtonBuilder()
      .setLabel('+1')
      .setStyle(ButtonStyle.Primary)
      .setCustomId('increment_btn')
  );
```

Útil para combos compactos de acción + etiqueta sin necesidad de un `ActionRowBuilder` completo.

### Thumbnail Spoiler

```js
new ThumbnailBuilder()
  .setURL('https://example.com/image.png')
  .setSpoiler(true) // se desenfoca hasta hacer clic
```

### Notas

- Una sección puede contener de **1 a 3** elementos `TextDisplayBuilder`
- El accesorio puede ser un `ThumbnailBuilder` **o** un `ButtonBuilder` — ambos están soportados
- `ThumbnailBuilder` solo acepta una URL — usa `attachment://filename` para archivos locales
- La sección puede estar en el primer nivel o dentro de un `ContainerBuilder`

---

## 6. MediaGallery

`MediaGalleryBuilder` muestra una o más imágenes o videos en una cuadrícula. Es el reemplazo de CV2 para `embed.setImage()` y las vistas previas de archivos adjuntos.

### Imagen Única

```js
const { MediaGalleryBuilder, MediaGalleryItemBuilder, MessageFlags } = require('discord.js');

const gallery = new MediaGalleryBuilder()
  .addItems(
    new MediaGalleryItemBuilder().setURL('https://example.com/banner.png')
  );

await channel.send({
  components: [gallery],
  flags: MessageFlags.IsComponentsV2,
});
```

### Múltiples Imágenes (Cuadrícula)

```js
const gallery = new MediaGalleryBuilder()
  .addItems(
    new MediaGalleryItemBuilder().setURL('https://example.com/image1.png').setDescription('Imagen 1'),
    new MediaGalleryItemBuilder().setURL('https://example.com/image2.png').setDescription('Imagen 2'),
    new MediaGalleryItemBuilder().setURL('https://example.com/image3.png').setDescription('Imagen 3')
  );
```

Las imágenes múltiples se renderizan como una cuadrícula en Discord.

### Con Texto Alternativo y Spoiler

```js
new MediaGalleryItemBuilder()
  .setURL('https://example.com/image.png')
  .setDescription('Una captura de pantalla del panel') // texto alternativo
  .setSpoiler(true) // se desenfoca hasta hacer clic
```

### Usando un Archivo Local

```js
const { AttachmentBuilder } = require('discord.js');

const file = new AttachmentBuilder('./assets/banner.png').setName('banner.png');

const gallery = new MediaGalleryBuilder()
  .addItems(
    new MediaGalleryItemBuilder().setURL('attachment://banner.png')
  );

await channel.send({
  components: [gallery],
  files: [file],
  flags: MessageFlags.IsComponentsV2,
});
```

### Ejemplo con Avatar de Usuario

```js
const gallery = new MediaGalleryBuilder()
  .addItems(
    new MediaGalleryItemBuilder()
      .setURL(interaction.user.displayAvatarURL({ size: 512 }))
      .setDescription(`Avatar de ${interaction.user.username}`)
  );
```

### Dentro de un Container

Las galerías suelen colocarse después de un separador para una separación visual limpia:

```js
const container = new ContainerBuilder()
  .addTextDisplayComponents(
    new TextDisplayBuilder().setContent('### Capturas de Pantalla')
  )
  .addSeparatorComponents(
    new SeparatorBuilder().setDivider(true).setSpacing(SeparatorSpacingSize.Small)
  )
  .addMediaGalleryComponents(
    new MediaGalleryBuilder().addItems(
      new MediaGalleryItemBuilder().setURL(imageUrl)
    )
  );
```

### Notas

- Hasta **10 elementos** por galería
- Soporta imágenes, videos y GIFs mediante URL
- Usa `attachment://filename` para archivos locales junto con `files: [attachment]`

---

## 7. File Component

`FileBuilder` te permite mostrar un archivo adjunto **en línea** dentro de un diseño CV2 — transcripciones, logs, exportaciones, lo que sea.

### Uso Básico

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

### Dentro de un Container

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

### Desde un Buffer

```js
const { Readable } = require('stream');

const content = 'Hola, este es el contenido del archivo.';
const stream = Readable.from([content]);
const file = new AttachmentBuilder(stream).setName('output.txt');
const fileComponent = new FileBuilder().setURL('attachment://output.txt');
```

### Enviar a los MDs de un Usuario

```js
await user.send({
  components: [fileComponent],
  files: [file],
  flags: MessageFlags.IsComponentsV2,
});
```

### Archivo Spoiler

```js
new FileBuilder().setURL('attachment://secret.txt').setSpoiler(true)
```

### Notas

- El nombre de archivo en `attachment://` debe **coincidir exactamente** con el nombre establecido en `AttachmentBuilder` — distingue mayúsculas/minúsculas
- Solo **un** `FileBuilder` por mensaje (limitación de Discord)
- Siempre llama a `.setName()` en el `AttachmentBuilder` para que el nombre de archivo sea explícito

---

## 8. ActionRow

`ActionRowBuilder` te permite colocar **botones y menús desplegables** dentro de un diseño CV2. En CV2, las action rows van dentro de un `ContainerBuilder` o en el primer nivel del array `components`.

### Botones Dentro de un Container

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

### Manejo de Clics en Botones

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

### Menú Desplegable Dentro de un Container

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

### Hasta 5 Botones por Fila

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

### Notas

- Cada `ActionRow` puede contener hasta **5 botones** o **1 menú desplegable**
- `custom_id` es obligatorio en todos los botones y selects (excepto `ButtonStyle.Link`)
- Las interacciones de botones se manejan mediante el evento `interactionCreate`

---

## 9. Botones

Los botones son componentes interactivos colocados dentro de un `ActionRowBuilder`. Cuando se hace clic en ellos, disparan un evento `interactionCreate` que tu bot maneja.

### Estilos de Botón

```js
const { ButtonBuilder, ButtonStyle } = require('discord.js');

new ButtonBuilder().setLabel('Primario').setStyle(ButtonStyle.Primary).setCustomId('btn_primary')
new ButtonBuilder().setLabel('Secundario').setStyle(ButtonStyle.Secondary).setCustomId('btn_secondary')
new ButtonBuilder().setLabel('Éxito').setStyle(ButtonStyle.Success).setCustomId('btn_success')
new ButtonBuilder().setLabel('Peligro').setStyle(ButtonStyle.Danger).setCustomId('btn_danger')
new ButtonBuilder().setLabel('Enlace').setStyle(ButtonStyle.Link).setURL('https://discord.gg/aerox')
```

> `ButtonStyle.Link` abre una URL — **no** dispara una interacción. No necesita `custom_id`.

### Botón Básico en un Mensaje CV2

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

### Manejar el Clic

```js
client.on('interactionCreate', async interaction => {
  if (!interaction.isButton()) return;

  if (interaction.customId === 'my_button') {
    await interaction.reply({ content: '¡Hiciste clic!', ephemeral: true });
  }
});
```

### Múltiples Botones en una Fila

```js
new ActionRowBuilder().addComponents(
  new ButtonBuilder().setLabel('Aceptar').setStyle(ButtonStyle.Success).setCustomId('accept'),
  new ButtonBuilder().setLabel('Rechazar').setStyle(ButtonStyle.Danger).setCustomId('decline'),
  new ButtonBuilder().setLabel('Info').setStyle(ButtonStyle.Secondary).setCustomId('info')
)
```

Hasta **5 botones** por fila. Para más, añade otro `ActionRowBuilder`.

### Botón Deshabilitado

```js
new ButtonBuilder()
  .setLabel('No disponible')
  .setStyle(ButtonStyle.Secondary)
  .setCustomId('disabled_btn')
  .setDisabled(true)
```

### Botón con Emoji

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

### Botón como Accesorio de Section

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

### Actualizar el Mensaje Después de un Clic

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

### Botones Persistentes (Sobreviven Reinicios del Bot)

Para botones que deben seguir funcionando después de que el bot se desconecte, siempre establece un `custom_id` y asegúrate de que tu manejador `interactionCreate` esté registrado al iniciar — Discord.js los reassigna automáticamente ya que escucha globalmente.

```js
// Mientras tu manejador interactionCreate se ejecute al iniciar, los botones persistentes funcionan.
client.on('interactionCreate', async interaction => {
  if (!interaction.isButton()) return;
  // manejar por customId...
});
```

### Notas

- Cada `ActionRow` puede contener hasta **5 botones**
- `custom_id` es obligatorio en todos los botones excepto `ButtonStyle.Link`
- `custom_id` puede tener hasta **100 caracteres** — úsalo para almacenar estado (ej. `close_ticket_42`)
- Los botones tienen un tiempo de espera de **15 minutos** a menos que los manejes como persistentes

---

## 10. Menús Desplegables (Select Menus)

Los menús desplegables permiten a los usuarios elegir una o más opciones de una lista. Van dentro de un `ActionRowBuilder` — un select por fila.

### Tipos de Menús Desplegables

| Builder | Qué Hace |
|---------|----------|
| `StringSelectMenuBuilder` | Lista personalizada de opciones que tú defines |
| `UserSelectMenuBuilder` | Elegir un usuario del servidor |
| `RoleSelectMenuBuilder` | Elegir un rol |
| `ChannelSelectMenuBuilder` | Elegir un canal |
| `MentionableSelectMenuBuilder` | Elegir un usuario o rol |

### String Select Menu

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

### Manejar la Selección

```js
client.on('interactionCreate', async interaction => {
  if (!interaction.isStringSelectMenu()) return;

  if (interaction.customId === 'category_select') {
    const chosen = interaction.values[0]; // ej. 'billing'
    await interaction.reply({ content: `Seleccionaste: **${chosen}**`, ephemeral: true });
  }
});
```

### Selecciones Múltiples Permitidas

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

### Opción con Emoji y Valor por Defecto

```js
new StringSelectMenuOptionBuilder()
  .setLabel('Facturación')
  .setValue('billing')
  .setDescription('Problemas de pago y suscripción')
  .setEmoji('💳')
  .setDefault(false)
```

### User Select Menu

Permite al usuario elegir a alguien del servidor — no necesita definir opciones:

```js
const { UserSelectMenuBuilder } = require('discord.js');

const select = new UserSelectMenuBuilder()
  .setCustomId('user_picker')
  .setPlaceholder('Elige un usuario...');

// Manejarlo:
client.on('interactionCreate', async interaction => {
  if (!interaction.isUserSelectMenu()) return;
  const pickedUser = interaction.users.first();
  await interaction.reply({ content: `Elegiste a ${pickedUser}`, ephemeral: true });
});
```

### Role Select Menu

```js
const { RoleSelectMenuBuilder } = require('discord.js');

const select = new RoleSelectMenuBuilder()
  .setCustomId('role_picker')
  .setPlaceholder('Elige un rol...');

// Manejarlo:
client.on('interactionCreate', async interaction => {
  if (!interaction.isRoleSelectMenu()) return;
  const pickedRole = interaction.roles.first();
  await interaction.reply({ content: `Elegiste ${pickedRole}`, ephemeral: true });
});
```

### Channel Select Menu

```js
const { ChannelSelectMenuBuilder, ChannelType } = require('discord.js');

const select = new ChannelSelectMenuBuilder()
  .setCustomId('channel_picker')
  .setPlaceholder('Elige un canal...')
  .addChannelTypes(ChannelType.GuildText); // solo mostrar canales de texto
```

### Menú Desplegable Deshabilitado

```js
new StringSelectMenuBuilder()
  .setCustomId('disabled_select')
  .setPlaceholder('No disponible ahora')
  .setDisabled(true)
  .addOptions(
    new StringSelectMenuOptionBuilder().setLabel('Opción').setValue('x')
  )
```

### Actualizar el Mensaje Después de una Selección

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

### Notas

- Solo **1 menú desplegable** por `ActionRow`
- Hasta **25 opciones** en un `StringSelectMenuBuilder`
- `custom_id` puede tener hasta **100 caracteres**
- Usa `interaction.values` para obtener los strings seleccionados; usa `interaction.users`, `interaction.roles` o `interaction.channels` para selects de entidades

---

## 11. Manejo de Interacciones

Cada clic en un botón o selección en un menú desplegable de un componente enviado por tu bot dispara una `interaction`, activando el evento `Client#interactionCreate`.

### Opciones de Manejo

- Esperar una sola interacción mediante `awaitMessageComponent()` en un canal o mensaje
- Escuchar múltiples interacciones durante un período usando un `InteractionCollector`
- Crear un manejador de componentes permanente en el evento `Client#interactionCreate`

### Responder a Interacciones de Componentes

Como con todas las interacciones, las interacciones de componentes de mensaje requieren una respuesta dentro de 3 segundos, o Discord las tratará como fallidas.

Al igual que los comandos slash, todos los tipos de interacciones de componentes de mensaje soportan los métodos `reply()`, `deferReply()`, `editReply()` y `followUp()`, con la opción de que estas respuestas sean efímeras.

Las interacciones de componentes también soportan dos métodos adicionales de respuesta:

#### update()

Responder a una interacción de componente mediante el método `update()` reconoce la interacción editando el mensaje en el que estaba adjunto el componente. Este método debería preferirse a llamar a `editReply()` en la interacción original que envió los componentes.

```js
await interaction.update({
  components: [newContainer],
  flags: MessageFlags.IsComponentsV2,
});
```

#### Deferred Updates

Responder mediante `deferUpdate()` reconoce la interacción y restablece el estado del mensaje. Este método puede usarse para suprimir la necesidad de más respuestas.

```js
await interaction.deferUpdate();
// ... hacer trabajo ...
await interaction.editReply({
  components: [newContainer],
  flags: MessageFlags.IsComponentsV2,
});
```

### Esperar Componentes (awaitMessageComponent)

Para situaciones donde esperas una sola respuesta (como una confirmación de botón):

```js
const response = await interaction.reply({
  content: '¿Estás seguro?',
  components: [row],
  withResponse: true,
});

const collectorFilter = (i) => i.user.id === interaction.user.id;

try {
  const confirmation = await response.resource.message.awaitMessageComponent({
    filter: collectorFilter,
    time: 60_000,
  });

  if (confirmation.customId === 'confirm') {
    await confirmation.update({ content: '¡Acción realizada!', components: [] });
  } else if (confirmation.customId === 'cancel') {
    await confirmation.update({ content: 'Acción cancelada', components: [] });
  }
} catch {
  await interaction.editReply({
    content: 'No se recibió confirmación en 1 minuto, cancelando',
    components: [],
  });
}
```

### Collectores de Componentes

Para situaciones donde quieres recopilar múltiples interacciones:

```js
const response = await interaction.reply({
  content: '¡Elige!',
  components: [row],
  withResponse: true,
});

const collector = response.resource.message.createMessageComponentCollector({
  componentType: ComponentType.StringSelect,
  time: 3_600_000, // 1 hora
});

collector.on('collect', async (i) => {
  const selection = i.values[0];
  await i.reply(`${i.user} seleccionó ${selection}!`);
});
```

### El Evento interactionCreate

Puedes tener un listener configurado para responder a características permanentes de botones o menús desplegables:

```js
const { Events } = require('discord.js');

module.exports = {
  name: Events.InteractionCreate,
  async execute(interaction) {
    if (interaction.isChatInputCommand()) {
      const command = interaction.client.commands.get(interaction.commandName);
      if (!command) {
        console.error(`No se encontró el comando ${interaction.commandName}`);
        return;
      }
      try {
        await command.execute(interaction);
      } catch (error) {
        console.error(`Error ejecutando ${interaction.commandName}`);
        console.error(error);
      }
    } else if (interaction.isButton()) {
      // responder al botón
    } else if (interaction.isStringSelectMenu()) {
      // responder al menú desplegable
    }
  },
};
```

---

## 12. Modals (Ventanas Emergentes)

Los modals son ventanas emergentes que te permiten solicitar información adicional al usuario. Esta respuesta de interacción en forma de formulario bloquea al usuario de interactuar con Discord hasta que el modal se envía o se descarta.

### Construir y Responder con Modals

Con la clase `ModalBuilder`, discord.js ofrece una forma conveniente de construir modals paso a paso.

Puedes tener un máximo de **cinco** componentes de primer nivel por modal, cada uno de los cuales puede ser un componente de etiqueta o de visualización de texto.

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

El `customId` es un string definido por el desarrollador de hasta 100 caracteres que identifica de forma única esta instancia del modal.

### Label (Etiqueta)

Los componentes Label envuelven a otros componentes del modal (entradas de texto, menús desplegables, etc.) para añadir una etiqueta y descripción.

```js
const { LabelBuilder, ModalBuilder } = require('discord.js');

client.on(Events.InteractionCreate, async (interaction) => {
  if (!interaction.isChatInputCommand()) return;
  if (interaction.commandName === 'ping') {
    const modal = new ModalBuilder().setCustomId('myModal').setTitle('Mi Modal');

    const hobbiesLabel = new LabelBuilder()
      .setLabel('¿Cuáles son tus pasatiempos favoritos?')
      .setDescription('Actividades que te gusta hacer');

    modal.addLabelComponents(hobbiesLabel);
  }
});
```

El campo `label` tiene un máximo de 45 caracteres. El campo `description` tiene un máximo de 100 caracteres.

### Text Input (Entrada de Texto)

Los componentes de entrada de texto solicitan al usuario texto libre de una o varias líneas.

```js
const { LabelBuilder, ModalBuilder, TextInputBuilder, TextInputStyle } = require('discord.js');

client.on(Events.InteractionCreate, async (interaction) => {
  if (!interaction.isChatInputCommand()) return;
  if (interaction.commandName === 'ping') {
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
  }
});
```

#### Estilos de Entrada

Discord ofrece dos estilos de entrada diferentes:

- `Short` — entrada de texto de una sola línea
- `Paragraph` — entrada de texto de múltiples líneas

#### Propiedades de Entrada

Un campo de entrada de texto puede personalizarse para aplicar validación:

```js
const input = new TextInputBuilder()
  .setId(1)
  .setMaxLength(1_000)
  .setMinLength(10)
  .setValue('Por defecto')
  .setRequired(true);
```

### Select Menu en Modal

Los menús desplegables también pueden usarse dentro de modals:

```js
const favoriteStarterSelect = new StringSelectMenuBuilder()
  .setCustomId('starter')
  .setPlaceholder('¡Haz una selección!')
  .setRequired(true)
  .addOptions(
    new StringSelectMenuOptionBuilder()
      .setLabel('Bulbasaur')
      .setDescription('El Pokémon semilla de tipo Planta/Veneno.')
      .setValue('bulbasaur'),
    new StringSelectMenuOptionBuilder()
      .setLabel('Charmander')
      .setDescription('El Pokémon lagartija de tipo Fuego.')
      .setValue('charmander'),
    new StringSelectMenuOptionBuilder()
      .setLabel('Squirtle')
      .setDescription('El Pokémon tortuga de tipo Agua.')
      .setValue('squirtle'),
  );

const favoriteStarterLabel = new LabelBuilder()
  .setLabel('¿Cuál es tu Pokémon inicial favorito de Gen 1?')
  .setStringSelectMenuComponent(favoriteStarterSelect);

modal.addLabelComponents(favoriteStarterLabel);
```

### Text Display en Modal

Los componentes TextDisplay ofrecen una forma de dar contexto adicional al usuario:

```js
const text = new TextDisplayBuilder().setContent(
  'Texto que no cabía en una etiqueta o descripción\n-# También se puede usar Markdown',
);

modal.addTextDisplayComponents(text);
```

### File Upload en Modal

Los componentes de carga de archivos permiten al usuario subir un archivo:

```js
const { FileUploadBuilder } = require('discord.js');

const pictureOfTheWeekUpload = new FileUploadBuilder().setCustomId('picture');

const pictureOfTheWeekLabel = new LabelBuilder()
  .setLabel('Foto de la Semana')
  .setDescription('Las mejores fotos que has tomado esta semana')
  .setFileUploadComponent(pictureOfTheWeekUpload);

modal.addLabelComponents(pictureOfTheWeekLabel);
```

#### Propiedades de File Upload

```js
const upload = new FileUploadBuilder()
  .setId(1)
  .setMinValues(1)
  .setMaxValues(1)
  .setRequired(true);
```

El campo `id` se usa para diferenciar componentes dentro de las interacciones. No puedes limitar y validar el **tamaño del archivo** o la **extensión del archivo**.

### Responder con un Modal

Una vez construido el modal, llama a `ChatInputCommandInteraction#showModal()` para mostrar el modal al usuario.

Mostrar un modal debe ser la **primera respuesta** a una interacción. **No puedes** hacer defer de modals.

```js
client.on(Events.InteractionCreate, async (interaction) => {
  if (!interaction.isChatInputCommand()) return;
  if (interaction.commandName === 'ping') {
    const modal = new ModalBuilder().setCustomId('myModal').setTitle('Mi Modal');

    const hobbiesLabel = new LabelBuilder()
      .setLabel('¿Cuáles son tus pasatiempos favoritos?')
      .setDescription('Actividades que te gusta hacer')
      .setTextInputComponent(
        new TextInputBuilder()
          .setCustomId('hobbiesInput')
          .setStyle(TextInputStyle.Short)
          .setPlaceholder('juegos de cartas, películas, libros, etc.')
      );

    modal.addLabelComponents(hobbiesLabel);

    await interaction.showModal(modal);
  }
});
```

### Recibir Envíos de Modal

#### El Evento interactionCreate

```js
client.on(Events.InteractionCreate, (interaction) => {
  if (!interaction.isModalSubmit()) return;
  console.log(interaction);
});
```

### Responder a Envíos de Modal

La clase `ModalSubmitInteraction` proporciona los mismos métodos que `ChatInputCommandInteraction`:

- `reply()`
- `editReply()`
- `deferReply()`
- `fetchReply()`
- `deleteReply()`
- `followUp()`

Si el modal se solicitó mediante un botón o menú desplegable, estos métodos pueden usarse para actualizar el mensaje subyacente:

- `update()`
- `deferUpdate()`

```js
client.on(Events.InteractionCreate, async (interaction) => {
  if (!interaction.isModalSubmit()) return;
  if (interaction.customId === 'myModal') {
    await interaction.reply({ content: '¡Tu envío fue recibido correctamente!' });
  }
});
```

### Extraer Datos de los Envíos de Modal

Puedes procesar los campos de entrada enviados mediante getters de conveniencia en `ModalSubmitInteraction#fields`:

```js
client.on(Events.InteractionCreate, async (interaction) => {
  if (!interaction.isModalSubmit()) return;
  if (interaction.customId === 'myModal') {
    await interaction.reply({ content: '¡Tu envío fue recibido!' });

    const hobbies = interaction.fields.getTextInputValue('hobbiesInput');
    const starter = interaction.fields.getStringSelectValues('starter');
    const picture = interaction.fields.getUploadedFiles('picture');

    console.log({ hobbies, starter, picture });
  }
});
```

Los envíos de entrada de texto vacíos devuelven un string vacío `""`. Los menús desplegables sin selección devuelven un array vacío `[]`.

---

## 13. Ejemplos del Mundo Real

Patrones completos y funcionales para casos de uso comunes de CV2 en Discord.js.

### 1. Notificación Efímera Simple

El patrón más común — enviar un mensaje corto al usuario:

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

### 2. Contenedor de Información con Título + Cuerpo

El patrón estándar de reemplazo de embed:

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

### 3. Panel de Control de Ticket (Section + Botones)

Un panel completo de ticket con miniatura de avatar y botones de acción:

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

### 4. Panel con Menú Desplegable

Un panel de ticket usando un menú desplegable para elegir categoría:

```js
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

### 5. Mensaje de Log con Archivo Adjunto

Enviar un log a un canal de staff incluyendo un archivo:

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

### 6. Mostrar Avatar con MediaGallery

Mostrar el avatar de un usuario dentro de un contenedor:

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

### 7. Comando Slash Completo — Todo Junto

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

### 8. Paginación Avanzada

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

---

## 14. Consejos y Errores Comunes

Errores frecuentes, límites y cosas que debes saber antes de publicar.

### Siempre Pasa la Bandera

Cada mensaje CV2 **debe** incluir `MessageFlags.IsComponentsV2`. Sin ella, los componentes de visualización no se renderizarán.

```js
await channel.send({
  components: [container],
  flags: MessageFlags.IsComponentsV2, // ← obligatorio cada vez
});
```

### Los Componentes Antiguos Siguen Funcionando

CV2 es **optativo por mensaje** — no tienes que migrar todo tu bot. Los mensajes antiguos con `ActionRow` siguen funcionando junto a CV2 en el mismo bot.

### Sin Embeds, Content, Stickers ni Polls Junto a CV2

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

### La Bandera No se Puede Eliminar

Una vez que un mensaje se envía con `IsComponentsV2`, la bandera **no puede eliminarse** de ese mensaje editándolo. Solo puedes editar el contenido de los componentes.

### Los Contenedores No se Pueden Anidar

No puedes poner un `ContainerBuilder` dentro de otro `ContainerBuilder`:

```
components: [
  ContainerBuilder   ✅
  ContainerBuilder   ✅
    └── ContainerBuilder  ❌ no permitido
]
```

### attachment:// Debe Coincidir Exactamente

```js
const file = new AttachmentBuilder('./report.txt').setName('report.txt');
new FileBuilder().setURL('attachment://report.txt')  // ✅

new FileBuilder().setURL('attachment://Report.txt')  // ❌ sensible a mayúsculas
```

Siempre llama a `.setName()` en `AttachmentBuilder` explícitamente para evitar discrepancias.

### Las Menciones Harán Ping — Usa allowedMentions

Las menciones de usuarios y roles dentro de `TextDisplayBuilder` **notificarán** a esos usuarios/roles:

```js
await channel.send({
  components: [new TextDisplayBuilder().setContent(`Hola <@${userId}>`)],
  flags: MessageFlags.IsComponentsV2,
  allowedMentions: { users: [] }, // suprimir pings
});
```

### Las Sections Aceptan 1–3 TextDisplays

Un `SectionBuilder` acepta entre **1 y 3** elementos `TextDisplayBuilder`. Más de 3 causará un error.

### Límites de Componentes

| Límite | Valor |
|--------|-------|
| **Componentes totales por mensaje** | **40 (incluyendo anidados)** |
| TextDisplays por sección | 1–3 |
| Botones por ActionRow | 5 |
| Opciones de select | 25 |
| Elementos en MediaGallery | 10 |
| Texto total en todos los TextDisplays | 4000 caracteres |

El límite de **40 componentes** es el más importante a vigilar — cada `ContainerBuilder`, `TextDisplayBuilder`, `SeparatorBuilder`, `SectionBuilder`, `ActionRowBuilder`, `ButtonBuilder`, etc. cuenta para ese límite, incluyendo los anidados.

---

> Guía original por **itsfizys** • Traducida y ampliada por ACHITODEV, con contenido de [discordjs.guide](https://discordjs.guide) • Servidor de Soporte: [discord.gg/aerox](https://discord.gg/aerox)
