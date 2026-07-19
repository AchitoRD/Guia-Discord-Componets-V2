# 07 - File Component

`ui.File` muestra un archivo adjunto **en linea** dentro de un layout CV2 - transcripciones, logs, etc.

## Uso basico

Necesitas:
1. Un `discord.File` (el archivo real a subir)
2. Un `ui.File` referenciandolo via `attachment://filename`

```python
file = discord.File("./transcript.txt", filename="transcript.txt")

container.add_item(ui.File(media="attachment://transcript.txt"))

await channel.send(view=layout, file=file)
```

## Desde un buffer en memoria

```python
import io

buffer = io.StringIO("Contenido del archivo.")
file = discord.File(buffer, filename="output.txt")

container.add_item(ui.File(media="attachment://output.txt"))

await channel.send(view=layout, file=file)
```

Importante: si ya escribiste en el buffer, haz seek(0) antes:

```python
buffer.seek(0)
```

## Enviar a MDs

```python
await user.send(view=layout, file=file)
```

## Dentro de un mensaje de log

```python
container.add_item(ui.TextDisplay("### Transcripcion del Ticket"))
container.add_item(ui.Separator())
container.add_item(ui.TextDisplay(f"Ticket cerrado por {user.mention}"))
container.add_item(ui.File(media="attachment://ticket-0042-transcript.txt"))

await log_channel.send(view=layout, file=discord.File("./ticket-0042-transcript.txt"))
```

## Notas

- El nombre en `attachment://` debe coincidir **exactamente** con el `filename` de `discord.File` (sensible a mayusculas)
- Solo **un** `discord.File` por `send()` (limitacion de Discord)
- Siempre haz `buffer.seek(0)` si reutilizas un buffer
