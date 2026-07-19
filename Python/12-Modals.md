# 12 - Modals

Los modals son ventanas emergentes que permiten solicitar informacion adicional al usuario.

Requiere discord.py 2.7+.

## Crear y mostrar un modal

```python
import discord
from discord import ui

class MiModal(ui.Modal, title="Mi Modal"):
    nombre = ui.TextInput(
        label="Nombre",
        placeholder="Tu nombre...",
        max_length=50,
        required=True
    )

    async def on_submit(self, interaction: discord.Interaction):
        await interaction.response.send_message(
            f"Recibido: {self.nombre.value}",
            ephemeral=True
        )

# Mostrar el modal
await interaction.response.send_modal(MiModal())
```

## Multiples campos

```python
class TicketModal(ui.Modal, title="Abrir Ticket"):
    titulo = ui.TextInput(
        label="Titulo",
        placeholder="Resumen del problema",
        style=discord.TextStyle.short,
        max_length=100
    )
    descripcion = ui.TextInput(
        label="Descripcion",
        placeholder="Describe tu problema en detalle...",
        style=discord.TextStyle.paragraph,
        max_length=1000
    )
    categoria = ui.TextInput(
        label="Categoria",
        placeholder="Soporte / Facturacion / Tecnico",
        style=discord.TextStyle.short,
        required=True
    )

    async def on_submit(self, interaction: discord.Interaction):
        await interaction.response.send_message(
            f"Ticket creado: {self.titulo.value}",
            ephemeral=True
        )
```

## Estilos de TextInput

- `discord.TextStyle.short` - una sola linea
- `discord.TextStyle.paragraph` - multiples lineas

## Select en modal

Los selects no se pueden usar directamente en modals de discord.py. Usa `TextInput` con estilo `short` o `paragraph`.

## FileUpload en modal

Los modals de discord.py no soportan subida de archivos directamente. Usa un comando separado o maneja archivos por DM.

## Manejar errores

```python
class MiModal(ui.Modal, title="Formulario"):
    nombre = ui.TextInput(label="Nombre")

    async def on_submit(self, interaction: discord.Interaction):
        await interaction.response.send_message(f"Hola {self.nombre.value}!", ephemeral=True)

    async def on_error(self, interaction: discord.Interaction, error: Exception):
        await interaction.response.send_message("Error en el formulario.", ephemeral=True)
        raise error
```

## Notas

- Maximo **5** componentes por modal
- Mostrar un modal debe ser la **primera respuesta** a una interaccion
- No puedes hacer defer de un modal
- Los campos de texto vacios devuelven string vacio ""
- El `label` tiene maximo 45 caracteres
