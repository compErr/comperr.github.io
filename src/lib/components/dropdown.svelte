<script lang="ts">
    import { LucideChevronRight } from "@lucide/svelte";
    import { slide } from "svelte/transition";
    import { expoInOut } from "svelte/easing";
    import type { Snippet } from "svelte";

    interface Props {
        name: string;
        content: Snippet;
    }

    let { name = "DefaultName", content }: Props = $props();
    let down = $state<boolean>(false);
    function handleClick() {
        down = !down;
    }
</script>

<div class="ide-block">
    <button class="struct-header" onclick={handleClick}>
        <span class="chevron" class:open={down}>
            <LucideChevronRight strokeWidth={1.5} size={16} color="#565f89" />
        </span>
        <span class="aqua">{name}</span>
        <span class="burple">struct</span>
        <span class="bracket">&lcub;</span>

        {#if !down}
            <span class="dots">...</span>
            <span class="bracket">&rcub;</span>
        {/if}
    </button>

    {#if down}
        <div
            class="struct-body"
            transition:slide={{ duration: 450, easing: expoInOut }}
        >
            <div class="content-wrapper">
                {@render content()}
            </div>

            <div class="bracket-close">
                <span class="bracket">&rcub;</span>
            </div>
        </div>
    {/if}
</div>

<style>
    .aqua {
        color: #16b9d7;
    }
    .burple {
        color: #bb9af7;
    }
    .bracket {
        color: #89ddff;
    }
    .dots {
        color: #565f89;
    }
    .ide-block {
        display: flex;
        flex-direction: column;
        width: 100%;
    }
    .ide-block :focus {
        outline: none;
    }
    .ide-block :focus-visible {
        outline: 1px solid #bb9af7;
        outline-offset: -1px;
        border-radius: 0.15rem;
    }
    .struct-header {
        background: transparent;
        border: none;
        font-family: inherit;
        font-size: inherit;
        display: flex;
        align-items: center;
        gap: 1ch;
        padding: 4px 24px 4px 40px;
        cursor: pointer;
        transition: background-color 0.1s ease;
        user-select: none;
        width: 100%;
        text-align: left;
    }
    .struct-header:hover {
        background-color: #292e42;
    }
    .chevron {
        display: flex;
        transition: transform 0.2s cubic-bezier(0.175, 0.885, 0.32, 1.275);
    }
    .chevron.open {
        transform: rotate(90deg);
    }
    .struct-body {
        display: flex;
        flex-direction: column;
    }
    .content-wrapper {
        margin-left: calc(40px + 16px + 1ch);
        padding-left: 20px;
        padding-top: 4px;
        padding-bottom: 4px;
        padding-right: 8px;
        border-left: 1.5px solid #565f89;
    }
    .bracket-close {
        padding-left: calc(40px + 15px + 1ch);
        padding-top: 4px;
        padding-bottom: 4px;
    }
</style>
