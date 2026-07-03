# 4. Separator

`SeparatorBuilder` añade una línea divisoria horizontal o espacio entre componentes. Ideal para separar un título del contenido del cuerpo o para dividir secciones visuales.

---

## Uso Básico

```js
const { SeparatorBuilder, SeparatorSpacingSize } = require('discord.js');

const separator = new SeparatorBuilder()
  .setDivider(true)
  .setSpacing(SeparatorSpacingSize.Small);
```

## Variantes de Espaciado

```js
new SeparatorBuilder().setSpacing(SeparatorSpacingSize.Small)
new SeparatorBuilder().setSpacing(SeparatorSpacingSize.Large)
```

`Small` añade un espacio pequeño. `Large` añade más espacio de respiro.

## Separador Invisible (Solo Espaciador)

Si quieres espacio sin la línea visible:

```js
new SeparatorBuilder().setDivider(false).setSpacing(SeparatorSpacingSize.Small)
```

Esto añade espacio en blanco entre componentes sin dibujar una línea.

## Dentro de un Container

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

## Notas

- Los separadores son puramente visuales — no contienen datos
- Puedes usar tantos separadores como necesites dentro de un contenedor
- Por defecto `setDivider(true)` muestra una línea visible; `false` es solo espacio
