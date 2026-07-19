# 09 - Buttons

Los botones van dentro de un `ui.ActionRow`. Usa la sintaxis de decorador para asignar callbacks.

Requiere discord.py 2.7+.

## Estilos de boton

```python
import discord

discord.ButtonStyle.primary    # blurple
discord.ButtonStyle.secondary  # grey
discord.ButtonStyle.success    # verde
discord.ButtonStyle.danger     # rojo
discord.ButtonStyle.link       # abre URL, no dispara callback
```

`ButtonStyle.link` abre una URL - no necesita `custom_id`, usa `url=` en su lugar.

## Boton basico en LayoutView

```python
import discord
from discord import ui

class MiLayout(discord.ui.LayoutView):
    row: discord.ui.ActionRow["MiLayout"] = discord.ui.ActionRow()

    @row.button(label="Click Me", style=discord.ButtonStyle.primary)
    async def mi_boton(self, interaction: discord.Interaction, button: discord.ui.Button):
        await interaction.response.send_message("Hiciste clic!", ephemeral=True)

@bot.tree.command()
async def hello(interaction: discord.Interaction):
    await interaction.response.send_message(view=MiLayout())
```

## Multiples botones

```python
class ConfirmLayout(discord.ui.LayoutView):
    row: discord.ui.ActionRow["ConfirmLayout"] = discord.ui.ActionRow()

    @row.button(label="Aceptar", style=discord.ButtonStyle.success)
    async def aceptar(self, interaction: discord.Interaction, button: discord.ui.Button):
        await interaction.response.send_message("Aceptado!")

    @row.button(label="Cancelar", style=discord.ButtonStyle.danger)
    async def cancelar(self, interaction: discord.Interaction, button: discord.ui.Button):
        await interaction.response.send_message("Cancelado.")
```

## Callbacks manuales (sin decorador)

```python
class MiLayout(discord.ui.LayoutView):
    def __init__(self):
        super().__init__()
        row = ui.ActionRow(
            ui.Button(label="Click", style=discord.ButtonStyle.primary, custom_id="btn")
        )
        row.children[0].callback = self.on_click
        container = ui.Container()
        container.add_item(row)
        self.add_item(container)

    async def on_click(self, interaction: discord.Interaction):
        await interaction.response.send_message("Clicked!", ephemeral=True)
```

## Notas

- Los botones Link no disparan interacciones - abren URL directamente
- `custom_id` es obligatorio excepto en botones Link
- Maximo 5 botones por ActionRow
- Botones persistentes necesitan `custom_id` + `bot.add_view(MiLayout())` al inicio
