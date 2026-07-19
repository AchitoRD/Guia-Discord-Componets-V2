# 13 - Ejemplos Completos

## 1. Notificacion efimera simple

```python
from discord import ui
import discord

layout = ui.LayoutView()
container = ui.Container(accent_color=None)
container.add_item(ui.TextDisplay("No tienes permisos para esto."))
layout.add_item(container)

await interaction.response.send_message(view=layout, ephemeral=True)
```

## 2. Info container con titulo + cuerpo

```python
layout = ui.LayoutView()
container = ui.Container(accent_color=None)
container.add_item(ui.TextDisplay("### Ticket Reabierto"))
container.add_item(ui.Separator())
container.add_item(ui.TextDisplay(f"Reabierto por {interaction.user.mention}"))
layout.add_item(container)

await interaction.response.send_message(view=layout)
```

## 3. Panel de control de ticket

```python
layout = ui.LayoutView(timeout=None)
container = ui.Container(accent_color=None)

container.add_item(ui.TextDisplay("# Ticket de Soporte"))
container.add_item(ui.Separator())

section = ui.Section(accessory=ui.Thumbnail(media=user.display_avatar.url))
section.add_item(ui.TextDisplay(
    f"**Bienvenido** {user.mention}\n"
    f"**Categoria:** {categoria}\n\n"
    "Nuestro equipo te atedera en breve."
))
container.add_item(section)
container.add_item(ui.Separator())

row = ui.ActionRow(
    ui.Button(label="Tomar", style=discord.ButtonStyle.primary, custom_id="ticket_claim"),
    ui.Button(label="Cerrar", style=discord.ButtonStyle.danger, custom_id="ticket_close")
)
container.add_item(row)
layout.add_item(container)

await channel.send(view=layout)
```

## 4. Panel con dropdown

```python
layout = ui.LayoutView(timeout=None)
container = ui.Container(accent_color=discord.Colour.blurple())

section = ui.Section(accessory=ui.Thumbnail(media=guild.icon.url))
section.add_item(ui.TextDisplay(f"**__{title}__**\n\n{description}"))
container.add_item(section)
container.add_item(ui.Separator())

options = [discord.SelectOption(label=name, value=name) for name, _ in categorias]
row = ui.ActionRow(
    ui.Select(placeholder="Selecciona una categoria...", options=options, custom_id="ticket_category")
)
container.add_item(row)
layout.add_item(container)

await channel.send(view=layout)
```

## 5. Log con archivo adjunto

```python
layout = ui.LayoutView()
container = ui.Container(accent_color=None)
container.add_item(ui.TextDisplay("### Logs - Ticket Eliminado"))
container.add_item(ui.Separator())
container.add_item(ui.TextDisplay(
    f"Ticket `#{num:04d}` eliminado por {interaction.user.display_name}\n"
    f"**Categoria:** {categoria}"
))
container.add_item(ui.Separator())
container.add_item(ui.File(media=f"attachment://ticket-{num:04d}-transcript.txt"))
layout.add_item(container)

await log_channel.send(view=layout, file=discord.File(f"./transcripts/ticket-{num:04d}-transcript.txt"))
```

## 6. Avatar con MediaGallery

```python
layout = ui.LayoutView()
container = ui.Container(accent_color=None)
container.add_item(ui.TextDisplay(f"### Avatar de {user.display_name}"))
container.add_item(ui.Separator())

gallery = ui.MediaGallery()
gallery.add_item(media=user.display_avatar.url)
container.add_item(gallery)

container.add_item(ui.Separator(divider=False))
container.add_item(ui.TextDisplay(f"[PNG]({user.display_avatar.with_format('png').url})"))
layout.add_item(container)

await interaction.response.send_message(view=layout, ephemeral=True)
```

## 7. Subclassing LayoutView

```python
class TicketConfirmView(ui.LayoutView):
    def __init__(self, bot, categoria, user_id):
        super().__init__(timeout=180)

        container = ui.Container(accent_colour=discord.Colour.blue())
        container.add_item(ui.TextDisplay("### Reglas de Soporte"))
        container.add_item(ui.Separator())
        container.add_item(ui.TextDisplay("Lee las reglas antes de abrir un ticket."))

        row = ui.ActionRow(
            ui.Button(label="Abrir Ticket", style=discord.ButtonStyle.primary, custom_id="confirm_open"),
            ui.Button(label="Cancelar", style=discord.ButtonStyle.danger, custom_id="cancel_ticket")
        )
        row.children[0].callback = self.confirmar
        row.children[1].callback = self.cancelar
        container.add_item(row)

        self.add_item(container)

    async def confirmar(self, interaction):
        await interaction.response.send_modal(TicketModal(...))

    async def cancelar(self, interaction):
        await interaction.response.edit_message(content="Cancelado.", view=None)

# Registrar view persistente
bot.add_view(TicketConfirmView(bot, "general", None))
```

## 8. Paginacion

```python
class PaginatedLayout(discord.ui.LayoutView):
    def __init__(self, items: list, page: int = 0):
        super().__init__(timeout=120)
        self.items = items
        self.page = page
        self._build()

    def _build(self):
        chunk = self.items[self.page * 5 : (self.page + 1) * 5]

        container = discord.ui.Container(accent_color=None)
        container.add_item(discord.ui.TextDisplay(f"# Pagina {self.page + 1}"))
        container.add_item(discord.ui.Separator())

        for item in chunk:
            container.add_item(discord.ui.TextDisplay(f"**{item['title']}**\n{item['desc']}"))

        buttons = []
        if self.page > 0:
            buttons.append(discord.ui.Button(label="Anterior", custom_id="prev_page", style=discord.ButtonStyle.secondary))
        if (self.page + 1) * 5 < len(self.items):
            buttons.append(discord.ui.Button(label="Siguiente", custom_id="next_page", style=discord.ButtonStyle.secondary))

        if buttons:
            row = ui.ActionRow(*buttons)
            row.children[0].callback = self.on_prev if self.page > 0 else None
            if len(buttons) > 1:
                row.children[1].callback = self.on_next
            elif self.page == 0 and buttons:
                row.children[0].callback = self.on_next
            container.add_item(row)

        self.add_item(container)

    async def on_prev(self, interaction):
        await interaction.response.edit_message(view=PaginatedLayout(self.items, self.page - 1))

    async def on_next(self, interaction):
        await interaction.response.edit_message(view=PaginatedLayout(self.items, self.page + 1))
```
