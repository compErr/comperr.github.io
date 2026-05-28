<script lang="ts">
    import { onMount } from "svelte";
    import { read, utils } from "xlsx";
    import { base } from "$app/paths";
    import Badge from "$lib/components/badge.svelte";

    const year = new Date().getFullYear();
    let allPagesData: Record<string, any[]> = $state({});
    let keys: string[] = $state([]);
    let headers: string[] = $state([]);
    let rows: any[] = $state([]);
    let loading = $state(true);
    let error = $state("");

    onMount(async () => {
        try {
            const response = await fetch(`${base}/data/Ratings.xlsx`);
            if (!response.ok)
                throw new Error(`Failed to fetch: ${response.statusText}`);

            const arrayBuffer = await response.arrayBuffer();
            const workbook = read(arrayBuffer);
            const extractedData: Record<string, any[]> = {};

            const sheetConfigs = [
                { name: workbook.SheetNames[2], range: "A30:CE53" },
                { name: workbook.SheetNames[3], range: "A1:O23" },
            ];

            sheetConfigs.forEach(({ name, range }) => {
                if (name && workbook.Sheets[name]) {
                    extractedData[name] = utils.sheet_to_json(
                        workbook.Sheets[name],
                        { range },
                    );
                }
            });

            allPagesData = extractedData;
            rows = allPagesData["Ratings"];

            keys = Object.keys(rows[0]);
            headers = keys.filter((key) => !key.includes("__EMPTY"));
            keys = keys.slice(5);
            loading = false;
        } catch (err) {
            console.error("Error loading Excel file:", err);
            error = "Failed to load Excel data.";
            loading = false;
        }
    });
</script>

<h1>F1 {year} Ratings</h1>

{#if loading}
    <p>Loading...</p>
{:else if error}
    <p>{error}</p>
{:else}
    <div class="table-wrapper">
        <div class="metadata">
            {#each ["Ranking", "Number", "Driver", "Team", "AVG"] as label}
                <div class="col">
                    <div class="col-header">{label}</div>
                    <div class="separator"></div>
                    <div class="sub-header-spacer"></div>
                    {#each rows.slice(1) as row, i}
                        {#if label === "AVG"}
                            <Badge rating={row[label].toFixed(2) ?? "—"} />
                        {:else if label === "Number"}
                            <div class="cell">{row["NO"]}</div>
                        {:else if label === "Driver"}
                            <div class="cell">
                                {row["DRIVER"].split(" ")[1]}
                            </div>
                        {:else if label === "Team"}
                            <div class="cell">{row["TEAM"]}</div>
                        {:else}
                            <div class="cell">
                                {label === "Ranking"
                                    ? i + 1
                                    : (row[label] ?? "—")}
                            </div>
                        {/if}
                    {/each}
                </div>
            {/each}
        </div>

        <div class="all-countries">
            {#each headers as country}
                {@const isSprint = rows[0][country] === "SQ"}
                <div class="country-group">
                    <div class="country-header">{country}</div>
                    <div class="separator"></div>
                    <div class="sub-cols" class:sprint={isSprint}>
                        {#each isSprint ? ["SQ", "SR", "Q", "R"] : ["Q", "R"] as sub}
                            <div class="col">
                                <div class="sub-header">{sub}</div>
                                {#each rows.slice(1) as row}
                                    <Badge rating={row[country]} />
                                {/each}
                            </div>
                        {/each}
                    </div>
                </div>
            {/each}
        </div>
    </div>
{/if}

<style>
    .table-wrapper {
        width: 100vw;
        position: relative;
        left: 50%;
        transform: translateX(-50%);
        padding: 0 3vw 0 0;
        box-sizing: border-box;
        display: flex;
        align-items: flex-start;
        overflow-x: auto;
    }

    .metadata {
        display: flex;
        flex-direction: row;
        gap: 16px;
        flex-shrink: 0;
        margin-right: 24px;
        position: sticky;
        left: 0;
        background-color: #14141a;
        z-index: 1;
        padding-right: 16px;
        padding-left: 3vw;
        border-right: 1px solid #565f89;
    }

    .col {
        display: flex;
        flex-direction: column;
        align-items: center;
        gap: 3px;
    }

    .col-header {
        margin-top: 14.5px;
    }
    .col-header,
    .sub-header {
        font-weight: bold;
        white-space: nowrap;
        text-align: center;
    }

    .cell {
        height: 28px;
        display: flex;
        align-items: center;
        justify-content: center;
        white-space: nowrap;
    }

    .separator {
        width: 100%;
        height: 1px;
        background-color: #565f89;
        margin: 2px 0 4px;
        flex-shrink: 0;
    }
    .all-countries {
        display: flex;
        gap: 24px;
    }

    .country-group {
        display: flex;
        flex-direction: column;
        align-items: center;
        flex-shrink: 0;
    }

    .country-header {
        font-weight: bold;
        text-align: center;
        white-space: nowrap;
    }

    .sub-cols {
        display: flex;
        gap: 12px;
    }
</style>
