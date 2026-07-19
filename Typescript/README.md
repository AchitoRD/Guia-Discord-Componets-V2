# discord.js Components V2 - Guia Completa (TypeScript)

Components V2 es el nuevo sistema de diseno de mensajes de Discord que reemplaza los embeds tradicionales.

## Requisito

discord.js >= 14.23.0

```bash
npm install discord.js@latest
```

## Indice

| # | Archivo | Tema |
|---|---------|------|
| 1 | [01-Message-Structure.md](01-Message-Structure.md) | Estructura de mensajes CV2 |
| 2 | [02-Container.md](02-Container.md) | Contenedor con color de acento |
| 3 | [03-TextDisplay.md](03-TextDisplay.md) | Renderizado de texto Markdown |
| 4 | [04-Separator.md](04-Separator.md) | Divisores visuales y espaciado |
| 5 | [05-Section-Thumbnail.md](05-Section-Thumbnail.md) | Texto + miniatura o boton |
| 6 | [06-MediaGallery.md](06-MediaGallery.md) | Cuadriculas de imagenes/videos |
| 7 | [07-File-Component.md](07-File-Component.md) | Archivos adjuntos en linea |
| 8 | [08-ActionRow.md](08-ActionRow.md) | Botones y selects dentro de CV2 |
| 9 | [09-Buttons.md](09-Buttons.md) | Componentes de boton |
| 10 | [10-Select-Menus.md](10-Select-Menus.md) | Menus de seleccion |
| 11 | [11-Interactions.md](11-Interactions.md) | Responder a clics y selecciones |
| 12 | [12-Modals.md](12-Modals.md) | Formularios emergentes |
| 13 | [13-Ejemplos.md](13-Ejemplos.md) | Patrones completos de produccion |
| 14 | [14-Consejos.md](14-Consejos.md) | Limites, errores y mejores practicas |

## Diferencia rapida: discord.js vs discord.py

| Concepto | discord.js (TypeScript) | discord.py |
|----------|------------------------|------------|
| Bandera | `MessageFlags.IsComponentsV2` | No necesaria |
| Contenedor | `new ContainerBuilder().setAccentColor(...)` | `ui.Container(accent_color=...)` |
| Texto | `new TextDisplayBuilder().setContent("...")` | `ui.TextDisplay("...")` |
| Separador | `new SeparatorBuilder().setDivider(true)` | `ui.Separator()` |
| Seccion | `new SectionBuilder().setThumbnailAccessory(...)` | `ui.Section(accessory=...)` |
| Boton | `new ButtonBuilder().setLabel(...).setStyle(...)` | `ui.Button(label=..., style=...)` |
| Select | `new StringSelectMenuBuilder().addOptions(...)` | `ui.Select(options=...)` |
| ActionRow | `new ActionRowBuilder<MessageActionRowComponentBuilder>()` | `ui.ActionRow(...)` |
| Envio | `interaction.reply({ components: [c], flags: MessageFlags.IsComponentsV2 })` | `interaction.response.send_message(view=layout)` |

---

Guia original por **itsfizys** - [discord.gg/aerox](https://discord.gg/aerox)
