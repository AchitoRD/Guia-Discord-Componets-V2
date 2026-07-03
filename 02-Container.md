# 2. Container

`ContainerBuilder` es el bloque de construcción principal de un diseño CV2. Agrupa componentes y opcionalmente añade una barra de acento de color en el lado izquierdo — similar a la franja de color de un embed de Discord.

---

## Uso Básico

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

## Con Color de Acento

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

## Sin Color (Predeterminado)

Simplemente no llames a `.setAccentColor()` — el contenedor se renderiza como un recuadro simple sin barra de acento.

## Contenedor Spoiler

Puedes desenfocar todo el contenedor hasta que el usuario haga clic:

```js
const container = new ContainerBuilder()
  .setSpoiler(true)
  .addTextDisplayComponents(
    new TextDisplayBuilder().setContent('¡Contenido oculto!')
  );
```

## Añadir Múltiples Componentes

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

## Notas

- Un mensaje puede contener **múltiples** contenedores en el primer nivel
- Los contenedores pueden contener: `TextDisplayBuilder`, `SeparatorBuilder`, `SectionBuilder`, `MediaGalleryBuilder`, `FileBuilder`, `ActionRowBuilder`
- Los contenedores **no pueden** anidarse dentro de otros contenedores
