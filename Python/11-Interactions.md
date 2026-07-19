# 11 - Interactions

Cada clic en un boton o seleccion en un menu dispara una interaccion que tu bot maneja.

## Responder a interacciones

Toda interaccion requiere respuesta dentro de **3 segundos**.

### Metodos de respuesta

```python
await interaction.response.send_message("mensaje")
await interaction.response.send_message("mensaje", ephemeral=True)
await interaction.response.defer()
await interaction.response.defer(ephemeral=True)
await interaction.response.edit_message(view=nuevo_layout)
```

### Despues de un defer

```python
await interaction.response.defer()
# ... trabajo async ...
await interaction.followup.send("Resultado:")
await interaction.edit_original_response(view=nuevo_layout)
```

## Manejador global interactionCreate

```python
@bot.event
async def on_interaction(interaction: discord.Interaction):
    if interaction.type == discord.InteractionType.component:
        # boton o select
        custom_id = interaction.data.get("custom_id", "")
        # manejar por custom_id
```

## Await de componente (esperar una sola respuesta)

```python
msg = await interaction.response.send_message(
    "Estas seguro?",
    view=mi_layout,
    ephemeral=True
)

try:
    interaccion = await bot.wait_for(
        "interaction",
        check=lambda i: i.user.id == interaction.user.id,
        timeout=60.0
    )
    await interaccion.response.edit_message(view=nuevo_layout)
except asyncio.TimeoutError:
    await interaction.edit_original_response(content="Tiempo agotado", view=None)
```

## Update vs Edit

`update` reemplaza el mensaje completo:

```python
await interaction.response.edit_message(view=nuevo_layout)
```

## Notas

- Las interacciones de botones expiran a los **15 minutos** si el bot se reinicia (a menos que sean persistentes)
- Usa `ephemeral=True` para respuestas privadas
- No puedes hacer defer de un modal
