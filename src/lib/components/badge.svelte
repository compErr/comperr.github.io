<script lang="ts">
    const colors = {
        blue: "#00b5ff",
        purple: "#b477ff",
        green: "#10b981",
        orange: "#fb923c",
        pink: "#fb7185",
        grey: "#5f6975",
    };

    type BadgeColor = keyof typeof colors;

    interface Props {
        text?: string;
        color?: BadgeColor;
        icon?: any;
        extraText?: string | null;
    }

    let {
        text = "Badge",
        color = "blue",
        icon: Icon = null,
        extraText = null,
    }: Props = $props();
</script>

<span
    class="badge"
    style:color={colors[color]}
    style:background-color="color-mix(in srgb, {colors[color]} 15%, transparent)"
    style:border="1.5px solid color-mix(in srgb, {colors[color]} 40%, transparent)"
>
    {#if Icon}
        <Icon size={16} strokeWidth={2.5} />
    {/if}
    <div>
        <span>{text}</span>
        {#if extraText}
            <span class="extra">{extraText}</span>
        {/if}
    </div>
</span>

<style>
    .extra {
        color: white;
        word-spacing: -0.6ch;
    }
    .badge {
        display: inline-flex;
        align-items: center;
        justify-content: center;
        gap: 0.375rem;
        padding: 0.2rem 0.7rem 0.2rem 0.7rem;
        font-size: 0.9rem;
        font-weight: 500;
        border-radius: 0.375rem;
    }
</style>
