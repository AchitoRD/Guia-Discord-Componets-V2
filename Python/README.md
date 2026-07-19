# discord.py Components V2 - Guia Completa

Components V2 es el nuevo sistema de diseno de mensajes de Discord que reemplaza los embeds tradicionales. Te da control total sobre el layout usando `discord.ui.LayoutView`.

## Requisito

discord.py desde GitHub (NO esta en PyPI):

```
pip install git+https://github.com/Rapptz/discord.py
```

Si ves `AttributeError: module 'discord.ui' has no attribute 'LayoutView'`, estas en la version incorrecta.

## Indice

| # | Archivo | Tema |
|---|---------|------|
| 1 | [01-LayoutView.md](01-LayoutView.md) | Raiz de todo mensaje CV2 |
| 2 | [02-Container.md](02-Container.md) | Contenedor con color de acento |
| 3 | [03-TextDisplay.md](03-TextDisplay.md) | Renderizado de texto Markdown |
| 4 | [04-Separator.md](04-Separator.md) | Divisores visuales y espaciado |
| 5 | [05-Section-Thumbnail.md](05-Section-Thumbnail.md) | Texto + miniatura o boton |
| 6 | [06-MediaGallery.md](06-MediaGallery.md) | Cuadriculas de imagenes |
| 7 | [07-File-Component.md](07-File-Component.md) | Archivos adjuntos en linea |
| 8 | [08-ActionRow.md](08-ActionRow.md) | Botones y selects dentro de CV2 |
| 9 | [09-Buttons.md](09-Buttons.md) | Componentes de boton |
| 10 | [10-Select-Menus.md](10-Select-Menus.md) | Menus de seleccion |
| 11 | [11-Interactions.md](11-Interactions.md) | Responder a clics y selecciones |
| 12 | [12-Modals.md](12-Modals.md) | Formularios emergentes |
| 13 | [13-Ejemplos.md](13-Ejemplos.md) | Patrones completos de produccion |
| 14 | [14-Consejos.md](14-Consejos.md) | Limites, errores y mejores practicas |

## Diferencia rapida: discord.py vs discord.js

| Concepto | discord.py | discord.js |
|----------|------------|------------|
| Raiz | `ui.LayoutView` | `ContainerBuilder` |
| Contenedor | `ui.Container(accent_color=...)` | `new ContainerBuilder().setAccentColor(...)` |
| Texto | `ui.TextDisplay("texto")` | `new TextDisplayBuilder().setContent("texto")` |
| Separador | `ui.Separator()` | `new SeparatorBuilder().setDivider(true)` |
| Seccion | `ui.Section(accessory=...)` | `new SectionBuilder().setThumbnailAccessory(...)` |
| Galeria | `ui.MediaGallery()` | `new MediaGalleryBuilder()` |
| Archivo | `ui.File(media="attachment://...")` | `new FileBuilder().setURL("attachment://...")` |
| Accion | `ui.ActionRow(...)` | `new ActionRowBuilder().addComponents(...)` |
| Boton | `ui.Button(label=..., style=..., custom_id=...)` | `new ButtonBuilder().setLabel(...)` |
| Select | `ui.Select(options=..., placeholder=...)` | `new StringSelectMenuBuilder().addOptions(...)` |
| Modal | `ui.Modal(title=..., custom_id=...)` | `new ModalBuilder().setTitle(...)` |
| Envio | `await interaction.response.send_message(view=layout)` | `await interaction.reply({ components: [...], flags: MessageFlags.IsComponentsV2 })` |

---

Guia original por **itsfizys** - [discord.gg/aerox](https://discord.gg/aerox)
