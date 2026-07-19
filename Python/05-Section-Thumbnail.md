# 05 - Section y Thumbnail

`ui.Section` coloca texto **lado a lado** con un accesorio - `ui.Thumbnail` (imagen) o `ui.Button`.

Requiere un accesorio. Si no tienes imagen ni boton, usa `TextDisplay` directamente en el container.

## Con miniatura

```python
section = ui.Section(accessory=ui.Thumbnail(media="https://example.com/avatar.png"))
section.add_item(ui.TextDisplay("**Juan Perez**\nTicket de soporte abierto."))
container.add_item(section)
```

## Avatar de usuario

```python
section = ui.Section(accessory=ui.Thumbnail(media=user.display_avatar.url))
section.add_item(ui.TextDisplay(f"**{user.display_name}**\nCategoria: General"))
container.add_item(section)
```

## Icono del servidor

```python
icon_url = interaction.guild.icon.url if interaction.guild.icon else None

if icon_url:
    section = ui.Section(accessory=ui.Thumbnail(media=icon_url))
    section.add_item(ui.TextDisplay(f"**__{title}__**\n\n{description}"))
    container.add_item(section)
else:
    container.add_item(ui.TextDisplay(f"**__{title}__**\n\n{description}"))
```

## Como se renderiza

```
+-----------------------------------------+
|  Texto aqui                       [img] |
|  Mas texto                              |
+-----------------------------------------+
```

La miniatura aparece a la derecha, texto a la izquierda.

## Boton como accesorio

```python
section = ui.Section(accessory=ui.Button(label="+1", custom_id="increment_btn"))
section.add_item(ui.TextDisplay("Count: 0", id=CONTADOR_ID))
container.add_item(section)
```

Util para combos compactos sin necesidad de un `ActionRow` completo.

## Notas

- `ui.Thumbnail` solo acepta URL string
- El accesorio es **OBLIGATORIO** - Section sin accesorio lanza error
- El accesorio puede ser `ui.Thumbnail` o `ui.Button`
- La seccion va dentro de un `Container`
