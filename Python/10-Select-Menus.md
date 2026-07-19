# 10 - Select Menus

Los menus desplegables van dentro de un `ui.ActionRow`. Un select por fila.

Requiere discord.py 2.7+.

## Tipos de Select Menu

| Clase | Funcion |
|-------|---------|
| `ui.Select` | Opciones personalizadas |
| `ui.UserSelect` | Elegir usuario del servidor |
| `ui.RoleSelect` | Elegir un rol |
| `ui.ChannelSelect` | Elegir un canal |
| `ui.MentionableSelect` | Elegir usuario o rol |

## String Select Menu

```python
import discord
from discord import ui

class MiLayout(discord.ui.LayoutView):
    container = discord.ui.Container(
        discord.ui.TextDisplay("Selecciona una categoria.")
    )
    row: discord.ui.ActionRow["MiLayout"] = discord.ui.ActionRow()

    @row.select(
        placeholder="Elige una categoria...",
        options=[
            discord.SelectOption(label="General",   value="general",   description="Soporte general"),
            discord.SelectOption(label="Facturacion", value="billing", description="Problemas de pago"),
            discord.SelectOption(label="Tecnico", value="technical", description="Ayuda tecnica"),
        ]
    )
    async def categoria_select(self, interaction: discord.Interaction, select: discord.ui.Select):
        seleccion = select.values[0]
        await interaction.response.send_message(f"Seleccionaste: {seleccion}", ephemeral=True)
```

## Seleccion multiple

```python
@row.select(
    placeholder="Elige hasta 3 roles",
    min_values=1,
    max_values=3,
    options=[
        discord.SelectOption(label="Anuncios", value="ann"),
        discord.SelectOption(label="Updates", value="upd"),
        discord.SelectOption(label="Eventos", value="evt"),
    ]
)
async def multi_select(self, interaction: discord.Interaction, select: discord.ui.Select):
    valores = select.values
    await interaction.response.send_message(f"Elegiste: {', '.join(valores)}", ephemeral=True)
```

## User Select Menu

```python
class MiLayout(discord.ui.LayoutView):
    row: discord.ui.ActionRow["MiLayout"] = discord.ui.ActionRow()

    @row.select(cls=discord.ui.UserSelect, placeholder="Elige un usuario...")
    async def user_picker(self, interaction: discord.Interaction, select: discord.ui.UserSelect):
        usuario = select.values[0]
        await interaction.response.send_message(f"Elegiste a {usuario.mention}", ephemeral=True)
```

## Channel Select Menu

```python
@row.select(cls=discord.ui.ChannelSelect, placeholder="Elige un canal...", channel_types=[discord.ChannelType.text])
async def channel_picker(self, interaction: discord.Interaction, select: discord.ui.ChannelSelect):
    canal = select.values[0]
    await interaction.response.send_message(f"Canal: {canal.mention}", ephemeral=True)
```

## Select deshabilitado

```python
ui.Select(
    placeholder="No disponible",
    options=[discord.SelectOption(label="Op", value="x")],
    disabled=True
)
```

## Notas

- Solo **1** select menu por ActionRow
- `select.values` devuelve una lista de valores seleccionados
- Para selects de entidad usa `select.values[0]` que devuelve el objeto directamente
- Hasta **25 opciones** en un `ui.Select`
