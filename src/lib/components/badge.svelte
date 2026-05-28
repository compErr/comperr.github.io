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
        text?: string | number;
        color?: BadgeColor;
        rating?: number | null; // Pass the raw rating here (0-10)
        icon?: any;
        extraText?: string | null;
        style?: string | null;
    }

    let {
        text = "Badge",
        color = "blue",
        rating = null,
        icon: Icon = null,
        extraText = null,
    }: Props = $props();

    let dynamicColor = $derived(
        rating !== null
            ? `hsl(${(Math.min(Math.max(rating, 0), 10) / 10) * 120}, 70%, 50%)`
            : colors[color],
    );
</script>

<span
    class="badge"
    class:fixed-size={rating !== null}
    style:color={dynamicColor}
    style:background-color="color-mix(in srgb, {dynamicColor} 15%, transparent)"
    style:border="1.5px solid color-mix(in srgb, {dynamicColor} 40%, transparent)"
>
    {#if Icon}
        <Icon size={16} strokeWidth={2.5} />
    {/if}
    <div>
        <span>{rating != null ? rating : text}</span>
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
        padding: 0.2rem 0.7rem;
        font-size: 0.9rem;
        font-weight: 500;
        border-radius: 0.375rem;
    }
    .fixed-size {
        width: 25px;
        height: 19px;
        font-weight: 800;
    }
</style>
