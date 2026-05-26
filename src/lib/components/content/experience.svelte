<script module>
    export { Work };
    const categoryColors = {
        languages: "blue",
        databases: "purple",
        ai: "orange",
        frameworks: "pink",
        testing: "grey",
    } as const;
    let shown = $state(false);
</script>

<script lang="ts">
    import Badge from "$lib/components/badge.svelte";
    import experience from "$lib/data/exp.json";
    import { slide } from "svelte/transition";
    import { expoOut } from "svelte/easing";
</script>

{#snippet exp(
    Title: string,
    Content: string[],
    Duration: string,
    Tags: {
        languages: string[];
        databases: string[];
        ai: string[];
        frameworks: string[];
        testing: string[];
    },
)}
    <div class="card">
        <div class="title">
            <span>{Title}</span>
            <div class="duration-row">
                <p class="duration">{Duration}</p>
                <button
                    class="details-toggle"
                    onclick={() => {
                        shown = !shown;
                    }}
                >
                    {shown ? "-" : "+"} details
                </button>
            </div>
        </div>
        {#if shown}
            <div transition:slide={{ duration: 250, easing: expoOut }}>
                {#if Content.length == 1}
                    <p class="content">{Content}</p>
                {:else}
                    <ul>
                        {#each Content as point}
                            <li>{point}</li>
                        {/each}
                    </ul>
                {/if}
            </div>
        {/if}
        <div class="tags">
            {#each Object.entries(Tags) as [category, list]}
                {#each list as tag}
                    <Badge
                        color={categoryColors[
                            category as keyof typeof categoryColors
                        ]}
                        text={tag}
                    ></Badge>
                {/each}
            {/each}
        </div>
    </div>
{/snippet}

{#snippet Work()}
    <div class="experience-container">
        {#each experience as job}
            {@render exp(job.title, job.content, job.duration, job.tech)}
        {/each}
    </div>
{/snippet}

<style>
    .duration-row {
        display: flex;
        align-items: center; /* Vertically centers the button with the text */
        gap: 15px; /* Adds a nice gap between the date and the button */
    }

    /* Update your existing details-toggle class to remove the bottom margin */
    .details-toggle {
        background: none;
        border: none;
        padding: 0;
        margin: 0; /* <-- Changed this to 0 so it aligns perfectly */
        color: #b477ff;
        font-family: inherit;
        font-size: 0.9rem;
        cursor: pointer;
        transition: color 0.2s ease;
    }

    .details-toggle:hover {
        color: #c0caf5; /* Highlights to the title color on hover */
    }
    .duration {
        font-style: italic;
        font-size: 15px;
        margin: 0px;
        font-weight: 300;
    }
    .tags {
        display: flex;
        flex-wrap: wrap;
        gap: 6px;
        margin-top: 10px;
    }
    .experience-container {
        display: flex;
        flex-direction: column;
        gap: 16px;

        /* 1. Badge Aesthetic Cloning Rules */
        color: #565f89;
        background-color: color-mix(in srgb, #565f89 15%, transparent);
        border: 1.5px solid color-mix(in srgb, #565f89 40%, transparent);
        border-radius: 0.5rem; /* Clean, matching rounded panel aesthetic */

        /* 2. Structured container padding adjustments */
        padding: 20px;
    }

    .card {
        margin-bottom: 8px;
    }

    .card:last-child {
        margin-bottom: 0; /* Clears bottom spacing overflow on the final element */
    }

    .title {
        display: flex;
        flex-direction: column;
        align-items: flex-start;
        gap: 10px;
        margin-top: 0;
        margin-bottom: 10px;
        color: #c0caf5;
        font-size: 1.15rem;
        font-weight: 700;
        line-height: 0.5rem;
    }

    .content {
        margin: 0;
        color: #a9b1d6;
        font-size: 0.95rem;
        line-height: 1.5;
    }

    .card ul {
        margin: 0;
        padding-left: 20px;
        color: #a9b1d6;
        font-size: 0.95rem;
        line-height: 1.6;
    }

    .card ul li {
        margin-bottom: 6px;
    }

    .card ul li:last-child {
        margin-bottom: 0;
    }
</style>
