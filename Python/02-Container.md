# 02 - Container

`ui.Container` es el bloque principal de un layout CV2. Agrupa componentes y opcionalmente anade una barra de acento de color a la izquierda.

## Uso basico

```python
container = ui.Container()
container.add_item(ui.TextDisplay("Hola!"))
layout.add_item(container)
```

## Con color de acento

```python
container = ui.Container(accent_color=discord.Colour.blurple())
```

Colores disponibles:

```python
ui.Container(accent_color=discord.Colour.green())
ui.Container(accent_color=discord.Colour.red())
ui.Container(accent_color=discord.Colour.from_rgb(255, 165, 0))
```

Ambas grafias funcionan: `accent_color` y `accent_colour`.

## Sin color

```python
container = ui.Container(accent_color=None)
```

Renderiza como un recuadro simple sin barra de acento.

## Multiples componentes

```python
container = ui.Container(accent_color=discord.Colour.blue())
container.add_item(ui.TextDisplay("### Titulo"))
container.add_item(ui.Separator())
container.add_item(ui.TextDisplay("Texto aqui."))
```

## Notas

- Un `LayoutView` puede tener **multiples** containers
- Containers pueden contener: `TextDisplay`, `Separator`, `Section`, `MediaGallery`, `File`, `ActionRow`
- Containers **NO** pueden anidarse dentro de otros containers
