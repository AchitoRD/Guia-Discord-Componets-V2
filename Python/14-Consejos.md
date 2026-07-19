# 14 - Consejos y Errores Comunes

## Instalar la version correcta

```bash
pip install git+https://github.com/Rapptz/discord.py
```

CV2 **no** esta en PyPI. Si ves `AttributeError: module 'discord.ui' has no attribute 'LayoutView'`, tienes la version incorrecta.

## Old ui.View sigue funcionando

CV2 es optativo por mensaje. Puedes tener mensajes con `ui.LayoutView` y otros con `ui.View` en el mismo bot.

## No embeds, content, stickers ni polls con CV2

```python
# Incorrecto
await channel.send(embed=my_embed, view=layout)
await channel.send(content="Hola", view=layout)

# Correcto - CV2 maneja todo
await channel.send(view=layout)
```

Para editar un mensaje no-CV2 a CV2, limpia `content` y `embeds` a `None`.

## Containers no se pueden anidar

```
LayoutView
+-- Container   OK
+-- Container   OK
    +-- Container  NO permitido
```

## accent_color vs accent_colour

Ambas grafias funcionan:

```python
ui.Container(accent_color=discord.Colour.red())   # OK
ui.Container(accent_colour=discord.Colour.red())  # OK
```

## Seek al buffer antes de enviar

```python
buffer.seek(0)
file = discord.File(buffer, filename="transcript.txt")
```

Olvidar esto envia un archivo vacio.

## attachment:// debe coincidir exactamente

```python
file = discord.File(buf, filename="report.txt")
container.add_item(ui.File(media="attachment://report.txt"))  # OK

container.add_item(ui.File(media="attachment://Report.txt"))  # ERROR - sensible a mayusculas
```

## Views persistentes necesitan custom_id

```python
bot.add_view(MiLayoutPersistente())
```

Sin `custom_id` y `add_view()`, los botones dejan de funcionar tras reinicio.

## Limite de componentes

| Componente | Limite |
|------------|--------|
| Componentes totales por LayoutView | **40** (incluyendo anidados) |
| Botones por ActionRow | 5 |
| Opciones de select | 25 |
| Imagenes en MediaGallery | 10 |
| Longitud TextDisplay | ~2000 chars por bloque |

El limite de 40 componentes es el mas importante. Cada `Container`, `TextDisplay`, `Separator`, `Section`, `ActionRow`, `Button`, etc. cuenta.

## Callbacks en ActionRow

No uses decoradores `@ui.button` dentro de un `LayoutView`. Asigna manualmente:

```python
row = ui.ActionRow(ui.Button(label="Click", custom_id="btn"))
row.children[0].callback = self.mi_funcion
```

## 3 segundos para responder

Toda interaccion debe recibir respuesta en **3 segundos**. Usa `defer()` si necesitas mas tiempo.
