# 04 - Separator

`ui.Separator` anade una linea divisoria horizontal entre componentes dentro de un container.

## Uso basico

```python
container.add_item(ui.TextDisplay("### Titulo"))
container.add_item(ui.Separator())
container.add_item(ui.TextDisplay("Contenido aqui."))
```

## Variantes de espaciado

```python
container.add_item(ui.Separator(spacing=discord.SeparatorSpacing.small))
container.add_item(ui.Separator(spacing=discord.SeparatorSpacing.large))
```

## Separador invisible (solo espacio)

```python
container.add_item(ui.Separator(divider=False))
```

Anade espacio en blanco sin dibujar linea.

## Patron comun

```python
container.add_item(ui.TextDisplay("### Ticket Cerrado"))
container.add_item(ui.Separator())
container.add_item(ui.TextDisplay(f"Cerrado por {user.mention}"))
container.add_item(ui.Separator())
container.add_item(ui.TextDisplay("Transcripcion enviada a tus MDs."))
```

## Notas

- Los separadores son puramente visuales - no llevan datos
- Puedes usar tantos separadores como quieras
