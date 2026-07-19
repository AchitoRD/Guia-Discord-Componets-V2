# 08 - ActionRow

`ui.ActionRow` coloca **botones y menus desplegables** dentro de un container. Reemplaza la forma antigua de anadir items a un `ui.View`.

## Botones en un container

```python
row = ui.ActionRow(
    ui.Button(label="Confirmar", style=discord.ButtonStyle.success, custom_id="confirm"),
    ui.Button(label="Cancelar", style=discord.ButtonStyle.danger, custom_id="cancel")
)

container.add_item(row)
```

## Asignar callbacks manualmente

Como no usas decoradores, asignas los callbacks manualmente:

```python
row = ui.ActionRow(
    ui.Button(label="Cerrar Ticket", style=discord.ButtonStyle.danger, custom_id="close_btn")
)
row.children[0].callback = self.cerrar_callback

container.add_item(row)
```

## Select menu en un container

```python
options = [
    discord.SelectOption(label="General", value="general"),
    discord.SelectOption(label="Facturacion", value="billing"),
]

row = ui.ActionRow(
    ui.Select(
        placeholder="Elige una categoria...",
        options=options,
        custom_id="category_select"
    )
)
row.children[0].callback = self.select_callback

container.add_item(row)
```

## Hasta 5 botones por fila

```python
buttons = [ui.Button(label=name, custom_id=f"btn_{name}") for name in categorias[:5]]
row = ui.ActionRow(*buttons)
container.add_item(row)
```

Para mas de 5 botones, usa multiples ActionRows:

```python
for i in range(0, len(buttons), 5):
    row = ui.ActionRow(*buttons[i:i+5])
    container.add_item(row)
```

## Notas

- Cada `ActionRow` soporta hasta **5 botones** o **1 select menu**
- No uses decoradores `@ui.button` dentro de un `LayoutView` - asigna callbacks manualmente
- `custom_id` es requerido para botones persistentes
- Accede a los hijos con `row.children[indice]`
