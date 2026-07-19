# 01 - LayoutView

`ui.LayoutView` es la raiz de todo mensaje Components V2. Todo componente CV2 vive dentro de un `LayoutView`. Se pasa como `view=` al enviar el mensaje.

## Uso basico

```python
from discord import ui
import discord

layout = ui.LayoutView()
layout.add_item(ui.Container())
await channel.send(view=layout)
```

## Responder a una interaccion

```python
await interaction.response.send_message(view=layout)
```

## Respuesta efimera

```python
await interaction.response.send_message(view=layout, ephemeral=True)
```

## Timeout

Por defecto `LayoutView` no tiene timeout (persistente). Puedes establecer uno:

```python
layout = ui.LayoutView(timeout=180)  # expira en 3 minutos
```

## Estilo clase (recomendado para layouts estaticos)

```python
class MiLayout(discord.ui.LayoutView):
    texto = discord.ui.TextDisplay("Hola, CV2!")
    contenedor = discord.ui.Container()
```

## find_item() - encontrar componentes anidados

Cada componente tiene una propiedad `id` (separada de `custom_id`). Puedes usarla para localizar y actualizar un componente especifico:

```python
CONTADOR_ID = 99

class ContadorLayout(discord.ui.LayoutView):
    container = discord.ui.Container(
        discord.ui.TextDisplay("Count: 0", id=CONTADOR_ID)
    )

async def callback(self, interaction):
    display = self.view.find_item(CONTADOR_ID)
    display.content = "Count: 1"
    await interaction.response.edit_message(view=self.view)
```

`find_item(id)` busca recursivamente en todos los componentes anidados.

## Notas

- CV2 (`LayoutView`) y mensajes antiguos (`ui.View`) pueden coexistir en el mismo bot
- NO puedes mezclar `LayoutView` con `ui.View` en el mismo mensaje
- `LayoutView` en si mismo es invisible - el contenido visual viene de los componentes internos
- Usa `timeout=None` para mensajes persistentes que sobreviven reinicios
- Limite: **40 componentes** por `LayoutView` (incluyendo anidados)
