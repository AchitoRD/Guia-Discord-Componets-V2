# 13 - Ejemplos Completos

## 1. Notificacion efimera simple

```typescript
import { ContainerBuilder, TextDisplayBuilder, MessageFlags } from 'discord.js';

const container = new ContainerBuilder()
    .addTextDisplayComponents(
        new TextDisplayBuilder().setContent("No tienes permisos."),
    );

await interaction.reply({
    components: [container],
    flags: MessageFlags.IsComponentsV2 | MessageFlags.Ephemeral,
});
```

## 2. Info container con titulo + cuerpo

```typescript
import { ContainerBuilder, TextDisplayBuilder, SeparatorBuilder, SeparatorSpacingSize, MessageFlags } from 'discord.js';

const container = new ContainerBuilder()
    .addTextDisplayComponents(
        new TextDisplayBuilder().setContent("### Ticket Reabierto"),
    )
    .addSeparatorComponents(
        new SeparatorBuilder().setDivider(true).setSpacing(SeparatorSpacingSize.Small),
    )
    .addTextDisplayComponents(
        new TextDisplayBuilder().setContent(`Reabierto por <@${interaction.user.id}>`),
    );

await interaction.reply({
    components: [container],
    flags: MessageFlags.IsComponentsV2,
});
```

## 3. Panel de control de ticket

```typescript
import { ContainerBuilder, TextDisplayBuilder, SeparatorBuilder, SeparatorSpacingSize,
    SectionBuilder, ThumbnailBuilder, ActionRowBuilder, ButtonBuilder, ButtonStyle, type MessageActionRowComponentBuilder, MessageFlags } from 'discord.js';

const container = new ContainerBuilder()
    .setAccentColor(0x5865F2)
    .addTextDisplayComponents(
        new TextDisplayBuilder().setContent("# Ticket de Soporte"),
    )
    .addSeparatorComponents(
        new SeparatorBuilder().setDivider(true).setSpacing(SeparatorSpacingSize.Small),
    )
    .addSectionComponents(
        new SectionBuilder()
            .addTextDisplayComponents(
                new TextDisplayBuilder().setContent(
                    `**Bienvenido** <@${user.id}>\n**Categoria:** ${category}\n\nTe atenderemos en breve.`,
                ),
            )
            .setThumbnailAccessory(
                new ThumbnailBuilder().setURL(user.displayAvatarURL()),
            ),
    )
    .addSeparatorComponents(
        new SeparatorBuilder().setDivider(true).setSpacing(SeparatorSpacingSize.Small),
    )
    .addActionRowComponents(
        new ActionRowBuilder<MessageActionRowComponentBuilder>()
            .addComponents(
                new ButtonBuilder().setLabel("Tomar").setStyle(ButtonStyle.Primary).setCustomId("ticket_claim"),
                new ButtonBuilder().setLabel("Cerrar").setStyle(ButtonStyle.Danger).setCustomId("ticket_close"),
            ),
    );

await channel.send({
    components: [container],
    flags: MessageFlags.IsComponentsV2,
});
```

## 4. Panel con dropdown

```typescript
import { StringSelectMenuBuilder, StringSelectMenuOptionBuilder, ActionRowBuilder, ContainerBuilder,
    TextDisplayBuilder, SeparatorBuilder, SeparatorSpacingSize, SectionBuilder, ThumbnailBuilder, type MessageActionRowComponentBuilder, MessageFlags } from 'discord.js';

const options = categorias.map(name =>
    new StringSelectMenuOptionBuilder().setLabel(name).setValue(name),
);

const container = new ContainerBuilder()
    .setAccentColor(0x5865F2)
    .addSectionComponents(
        new SectionBuilder()
            .addTextDisplayComponents(
                new TextDisplayBuilder().setContent(`**__${title}__**\n\n${description}`),
            )
            .setThumbnailAccessory(
                new ThumbnailBuilder().setURL(interaction.guild.iconURL()!),
            ),
    )
    .addSeparatorComponents(
        new SeparatorBuilder().setDivider(true).setSpacing(SeparatorSpacingSize.Small),
    )
    .addActionRowComponents(
        new ActionRowBuilder<MessageActionRowComponentBuilder>()
            .addComponents(
                new StringSelectMenuBuilder()
                    .setCustomId("ticket_category")
                    .setPlaceholder("Selecciona una categoria...")
                    .addOptions(...options),
            ),
    );

await channel.send({
    components: [container],
    flags: MessageFlags.IsComponentsV2,
});
```

## 5. Log con archivo adjunto

```typescript
import { AttachmentBuilder, FileBuilder, ContainerBuilder, TextDisplayBuilder, SeparatorBuilder, SeparatorSpacingSize, MessageFlags } from 'discord.js';

const filename = `ticket-${String(num).padStart(4, "0")}-transcript.txt`;
const file = new AttachmentBuilder(`./transcripts/${filename}`).setName(filename);

const container = new ContainerBuilder()
    .addTextDisplayComponents(
        new TextDisplayBuilder().setContent("### Logs - Ticket Eliminado"),
    )
    .addSeparatorComponents(
        new SeparatorBuilder().setDivider(true).setSpacing(SeparatorSpacingSize.Small),
    )
    .addTextDisplayComponents(
        new TextDisplayBuilder().setContent(
            `Ticket #${String(num).padStart(4, "0")} eliminado por ${interaction.user.displayName}\n**Categoria:** ${category}`,
        ),
    )
    .addSeparatorComponents(
        new SeparatorBuilder().setDivider(true).setSpacing(SeparatorSpacingSize.Small),
    )
    .addFileComponents(
        new FileBuilder().setURL(`attachment://${filename}`),
    );

await logChannel.send({
    components: [container],
    files: [file],
    flags: MessageFlags.IsComponentsV2,
});
```

## 6. Avatar con MediaGallery

```typescript
const avatarUrl = user.displayAvatarURL({ size: 1024, extension: "png" });

const container = new ContainerBuilder()
    .addTextDisplayComponents(
        new TextDisplayBuilder().setContent(`### Avatar de ${user.displayName}`),
    )
    .addSeparatorComponents(
        new SeparatorBuilder().setDivider(true).setSpacing(SeparatorSpacingSize.Small),
    )
    .addMediaGalleryComponents(
        new MediaGalleryBuilder().addItems(
            new MediaGalleryItemBuilder().setURL(avatarUrl).setDescription("Avatar"),
        ),
    )
    .addSeparatorComponents(
        new SeparatorBuilder().setDivider(false).setSpacing(SeparatorSpacingSize.Small),
    )
    .addTextDisplayComponents(
        new TextDisplayBuilder().setContent(`[PNG](${avatarUrl})`),
    );

await interaction.reply({
    components: [container],
    flags: MessageFlags.IsComponentsV2 | MessageFlags.Ephemeral,
});
```

## 7. Comando slash completo

```typescript
import { SlashCommandBuilder, MessageFlags, ContainerBuilder, TextDisplayBuilder,
    SeparatorBuilder, SeparatorSpacingSize, SectionBuilder, ThumbnailBuilder,
    MediaGalleryBuilder, MediaGalleryItemBuilder, ActionRowBuilder, ButtonBuilder,
    ButtonStyle, type MessageActionRowComponentBuilder } from 'discord.js';

export const data = new SlashCommandBuilder()
    .setName("showcase")
    .setDescription("Demo CV2");

export async function execute(interaction: any) {
    const avatar = interaction.client.user.displayAvatarURL({ extension: "png", size: 512 });

    const container = new ContainerBuilder()
        .setAccentColor(0x5865F2)
        .addTextDisplayComponents(
            new TextDisplayBuilder().setContent("# Components V2 Demo"),
        )
        .addSeparatorComponents(
            new SeparatorBuilder().setDivider(true).setSpacing(SeparatorSpacingSize.Small),
        )
        .addSectionComponents(
            new SectionBuilder()
                .addTextDisplayComponents(
                    new TextDisplayBuilder().setContent("**Bot Info**"),
                    new TextDisplayBuilder().setContent("Miniatura + texto."),
                )
                .setThumbnailAccessory(
                    new ThumbnailBuilder().setURL(avatar).setDescription("Bot avatar"),
                ),
        )
        .addSeparatorComponents(
            new SeparatorBuilder().setDivider(true).setSpacing(SeparatorSpacingSize.Small),
        )
        .addMediaGalleryComponents(
            new MediaGalleryBuilder().addItems(
                new MediaGalleryItemBuilder().setURL("https://picsum.photos/seed/a/400/300"),
                new MediaGalleryItemBuilder().setURL("https://picsum.photos/seed/b/400/300"),
            ),
        )
        .addSeparatorComponents(
            new SeparatorBuilder().setDivider(true).setSpacing(SeparatorSpacingSize.Small),
        )
        .addActionRowComponents(
            new ActionRowBuilder<MessageActionRowComponentBuilder>()
                .addComponents(
                    new ButtonBuilder().setLabel("Click").setStyle(ButtonStyle.Primary).setCustomId("demo_btn"),
                ),
        )
        .addTextDisplayComponents(
            new TextDisplayBuilder().setContent("-# Powered by Components V2"),
        );

    await interaction.reply({
        components: [container],
        flags: MessageFlags.IsComponentsV2,
    });
}
```

## 8. Paginacion

```typescript
function buildPage(items: any[], page = 0, pageSize = 5) {
    const chunk = items.slice(page * pageSize, (page + 1) * pageSize);

    const container = new ContainerBuilder()
        .addTextDisplayComponents(
            new TextDisplayBuilder().setContent(`# Pagina ${page + 1}`),
        )
        .addSeparatorComponents(
            new SeparatorBuilder().setDivider(true).setSpacing(SeparatorSpacingSize.Small),
        );

    chunk.forEach((item: any) => {
        container.addTextDisplayComponents(
            new TextDisplayBuilder().setContent(`**${item.title}**\n${item.desc}`),
        );
    });

    const buttons: ButtonBuilder[] = [];
    if (page > 0)
        buttons.push(new ButtonBuilder().setCustomId("prev").setLabel("Anterior").setStyle(ButtonStyle.Secondary));
    if ((page + 1) * pageSize < items.length)
        buttons.push(new ButtonBuilder().setCustomId("next").setLabel("Siguiente").setStyle(ButtonStyle.Secondary));

    if (buttons.length)
        container.addActionRowComponents(
            new ActionRowBuilder<MessageActionRowComponentBuilder>()
                .addComponents(...buttons),
        );

    return container;
}
```

## 9. Modal + FileUpload + MediaGallery

```typescript
// Ver seccion 12-Modals.md para el ejemplo completo
```
