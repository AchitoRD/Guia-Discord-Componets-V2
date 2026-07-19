# 06 - MediaGallery

`ui.MediaGallery` muestra una o mas imagenes en cuadricula dentro de un container.

## Imagen unica

```python
gallery = ui.MediaGallery()
gallery.add_item(media="https://example.com/banner.png")
container.add_item(gallery)
```

## Multiples imagenes

```python
gallery = ui.MediaGallery()
gallery.add_item(media="https://example.com/imagen1.png")
gallery.add_item(media="https://example.com/imagen2.png")
gallery.add_item(media="https://example.com/imagen3.png")
container.add_item(gallery)
```

## Usando MediaGalleryItem directamente

```python
item = discord.ui.MediaGalleryItem(media="https://example.com/avatar.png")
gallery = ui.MediaGallery(item)
container.add_item(gallery)
```

## Avatar de usuario

```python
gallery = ui.MediaGallery()
gallery.add_item(media=user.display_avatar.url)
container.add_item(gallery)
```

## Con archivo local

```python
gallery = ui.MediaGallery()
gallery.add_item(media="attachment://banner.png")
container.add_item(gallery)

await channel.send(view=layout, file=discord.File("./assets/banner.png", filename="banner.png"))
```

## Patron de colocacion

Las galerias suelen ir despues de un separador:

```python
container.add_item(ui.Separator())
gallery = ui.MediaGallery()
gallery.add_item(media=image_url)
container.add_item(gallery)
```

## Notas

- Solo acepta URLs string (no rutas de archivo locales directas)
- Para archivos locales usa `attachment://filename` + `discord.File`
- Hasta **10 imagenes** por galeria
