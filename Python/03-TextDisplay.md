# 03 - TextDisplay

`ui.TextDisplay` renderiza texto Markdown dentro de un layout CV2. Es la forma principal de mostrar cualquier contenido escrito.

## Uso basico

```python
container.add_item(ui.TextDisplay("Hola, mundo!"))
```

## Markdown soportado

```python
container.add_item(ui.TextDisplay("### Ticket Abierto"))
container.add_item(ui.TextDisplay("**Estado:** Abierto"))
container.add_item(ui.TextDisplay(">>> Esto es un blockquote"))
container.add_item(ui.TextDisplay("`codigo inline` funciona"))
```

## Texto multilinea

```python
container.add_item(ui.TextDisplay(
    "**Usuario:** Juan\n"
    "**Categoria:** Soporte\n"
    "**Creado:** <t:1700000000:R>"
))
```

## Timestamps de Discord

```python
import discord

ts = int(discord.utils.utcnow().timestamp())

container.add_item(ui.TextDisplay(f"Abierto <t:{ts}:R>"))
container.add_item(ui.TextDisplay(f"Abierto <t:{ts}:F>"))
```

## Menciones

```python
container.add_item(ui.TextDisplay(f"Creado por {user.mention}"))
container.add_item(ui.TextDisplay(f"Canal: {channel.mention}"))
```

## Notas

- Puedes tener tantos `TextDisplay` como quieras en un container
- No hay separacion titulo/descripcion como en embeds - usa headings de markdown
- Los emojis (unicode y personalizados) se renderizan bien
