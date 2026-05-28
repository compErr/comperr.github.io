<script lang="ts">
    import type { Pathname } from "$app/types";
    import { resolve } from "$app/paths";
    import { page } from "$app/state";
    import { locales, localizeHref } from "$lib/paraglide/runtime";
    import favicon from "$lib/assets/favicon.svg";
    import "@fontsource-variable/jetbrains-mono/wght.css";
    let { children } = $props();
    export const prerender = true;
</script>

<svelte:head><link rel="icon" href={favicon} /></svelte:head>
{@render children()}

<div style="display:none">
    {#each locales as locale (locale)}
        <a
            href={resolve(
                localizeHref(page.url.pathname, { locale }) as Pathname,
            )}>{locale}</a
        >
    {/each}
</div>

<style>
    :global(body) {
        margin-left: 20%;
        margin-right: 20%;
        font-family: "JetBrains Mono Variable", monospace;
        background-color: #14141a;
        color: #b7c0e9;
    }

    :global(html) {
        scrollbar-gutter: stable;
        scrollbar-color: #565f89 transparent;
        scrollbar-width: thin;
        overflow-x: hidden;
    }

    :global(::-webkit-scrollbar) {
        width: 10px;
    }

    :global(::-webkit-scrollbar-track) {
        background: transparent;
    }

    :global(::-webkit-scrollbar-thumb) {
        background-color: #565f89;
        border-radius: 8px;
        border: 2px solid transparent;
        background-clip: padding-box;
    }

    :global(::-webkit-scrollbar-thumb:hover) {
        background-color: #b477ff;
    }

    @media (max-width: 768px) {
        :global(body) {
            margin-left: 5%;
            margin-right: 5%;
        }
    }
</style>
