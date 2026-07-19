# 04 - Separator

`SeparatorBuilder` anade linea divisoria o espacio entre componentes.

## Uso basico

```typescript
import { SeparatorBuilder, SeparatorSpacingSize } from 'discord.js';

const separator = new SeparatorBuilder()
    .setDivider(true)
    .setSpacing(SeparatorSpacingSize.Small);
```

## Variantes de espaciado

```typescript
new SeparatorBuilder().setSpacing(SeparatorSpacingSize.Small);
new SeparatorBuilder().setSpacing(SeparatorSpacingSize.Large);
```

## Separador invisible (solo espacio)

```typescript
new SeparatorBuilder().setDivider(false).setSpacing(SeparatorSpacingSize.Small);
```

## Dentro de un container

```typescript
import { ContainerBuilder, TextDisplayBuilder, SeparatorBuilder, SeparatorSpacingSize } from 'discord.js';

const container = new ContainerBuilder()
    .addTextDisplayComponents(
        new TextDisplayBuilder().setContent("### Ticket Cerrado"),
    )
    .addSeparatorComponents(
        new SeparatorBuilder().setDivider(true).setSpacing(SeparatorSpacingSize.Small),
    )
    .addTextDisplayComponents(
        new TextDisplayBuilder().setContent(`Cerrado por <@${user.id}>`),
    )
    .addSeparatorComponents(
        new SeparatorBuilder().setDivider(true).setSpacing(SeparatorSpacingSize.Small),
    )
    .addTextDisplayComponents(
        new TextDisplayBuilder().setContent("Transcripcion enviada."),
    );
```

## Notas

- Los separadores son puramente visuales
- Por defecto `setDivider(true)` muestra linea; `false` es solo espacio
