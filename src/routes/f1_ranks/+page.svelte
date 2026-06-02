<script lang="ts">
    import { onMount } from "svelte";
    import { read, utils } from "xlsx";
    import { base } from "$app/paths";
    import Badge from "$lib/components/badge.svelte";
    import { scale } from "svelte/transition";

    const year = new Date().getFullYear();
    let allPagesData: Record<string, any[]> = $state({});
    let keys: string[] = $state([]);
    let headers: string[] = $state([]);
    let rows: any[] = $state([]);
    let loading = $state(true);
    let error = $state("");
    let showMode = $state(1);
    let showAll = $state(true);
    let rowData = $derived(showAll ? rows.slice(1) : rows.slice(1, 11));

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

            if (rows && rows.length > 0) {
                keys = Object.keys(rows[1] || {});
                headers = Object.keys(rows[0] || {}).filter(
                    (key) => !key.includes("__EMPTY"),
                );
            }
            loading = false;
        } catch (err) {
            console.error("Error loading Excel file:", err);
            error = "Failed to load Excel data.";
            loading = false;
        }
    });

    const teamColors: Record<string, string> = {
        Mercedes: "#26F0CE",
        McLaren: "#FC7F00",
        Ferrari: "#E5002C",
        "Red Bull": "#356FC3",
        Alpine: "#00A0E6",
        "Racing Bulls": "#608CFB",
        Haas: "#DDE0E1",
        Williams: "#1766D7",
        Audi: "#FD2C00",
        Cadillac: "#A7A7AA",
        "Aston Martin": "#229870",
    };
</script>

<h1 class="title">F1 {year} Ratings</h1>

{#if loading}
    <p>Loading...</p>
{:else if error}
    <p>{error}</p>
{:else}
    <div class="inline">
        <div class="selector" style="--active: {showMode}; --count: 3;">
            <button
                class:selected={showMode == 0}
                onclick={() => {
                    showMode = 0;
                }}
            >
                All Sessions
            </button>
            <button
                class:selected={showMode == 1}
                onclick={() => {
                    showMode = 1;
                }}>Only Average</button
            >
            <button
                class:selected={showMode == 2}
                onclick={() => {
                    showMode = 2;
                }}>Only Overall</button
            >
        </div>

        <div
            class="selector top"
            style="--active: {showAll ? 0 : 1}; --count: 2;"
        >
            <button
                class:selected={showAll}
                onclick={() => {
                    showAll = true;
                }}>All</button
            >
            <button
                class:selected={!showAll}
                onclick={() => {
                    showAll = false;
                }}>Top 10</button
            >
        </div>
    </div>

    <div
        class="outer-wrapper"
        transition:scale={{ duration: 500, start: 0.95 }}
    >
        <div class="scroll-wrapper">
            <table>
                <thead>
                    <tr>
                        <th rowspan="2" class="rank">RANK</th>
                        <th rowspan="2" class="number">NO</th>
                        <th rowspan="2" class="driver">DRIVER</th>
                        <th rowspan="2" class="team">TEAM</th>
                        <th rowspan="2" class="avg">AVG</th>

                        {#if !(showMode == 2)}
                            {#each headers as header}
                                {@const isSprint = rows[0][header] === "SQ"}
                                <th
                                    colspan={showMode == 1
                                        ? 1
                                        : isSprint
                                          ? 5
                                          : 3}
                                    class="race-name"
                                >
                                    {header}
                                </th>
                            {/each}
                        {/if}
                    </tr>
                    {#if !(showMode == 2)}
                        <tr>
                            {#if !(showMode == 1)}
                                {#each headers as header}
                                    {@const isSprint = rows[0][header] === "SQ"}
                                    {#each isSprint ? ["SQ", "SR", "Q", "R", "AVG"] : ["Q", "R", "AVG"] as sub}
                                        <th class="sub-header">{sub}</th>
                                    {/each}
                                {/each}
                            {/if}
                        </tr>
                    {/if}
                </thead>
                <tbody transition:scale={{ duration: 600, start: 0.95 }}>
                    {#each rowData as row, i (row["DRIVER"])}
                        {@const name = row["DRIVER"].split(" ")}
                        {@const team = row["TEAM"]}

                        <tr>
                            <td class="rank">{i + 1}</td>
                            <td class="number">
                                <div
                                    class="box"
                                    style:background-color={teamColors[team]}
                                >
                                    {row["NO"]}
                                </div>
                            </td>
                            <td class="driver">{`${name[0][0]}.${name[1]}`}</td>
                            <td class="team">
                                <div
                                    class="team-box"
                                    style:background-color={teamColors[team]}
                                >
                                    {team === "Racing Bulls"
                                        ? "VCARB"
                                        : team === "Aston Martin"
                                          ? "AMR"
                                          : team}
                                </div>
                            </td>
                            <td class="avg">
                                <Badge
                                    rating={typeof row["AVG"] === "number"
                                        ? row["AVG"].toFixed(2)
                                        : row["AVG"]}
                                />
                            </td>
                            {#if !(showMode == 2)}
                                {#each keys.slice(5) as key, idx}
                                    {@const nxtKey = keys.slice(5)[idx + 1]}
                                    {@const isAvg =
                                        !nxtKey || !nxtKey.includes("__EMPTY")}

                                    {#if !(showMode == 1) || isAvg}
                                        <td class="badge-cell">
                                            <Badge
                                                rating={typeof row[key] ===
                                                "number"
                                                    ? row[key].toFixed(2)
                                                    : row[key]}
                                            />
                                        </td>
                                    {/if}
                                {/each}
                            {/if}
                        </tr>
                    {/each}
                </tbody>
            </table>
        </div>
    </div>
{/if}

<style>
    .inline {
        display: flex;
        justify-content: center;
        align-items: center;
        gap: 20px;
        margin-bottom: 15px;
    }
    .selector {
        background-color: color-mix(in srgb, white 8%, transparent);
        border: 1.5px solid color-mix(in srgb, white 20%, transparent);
        display: grid;
        grid-template-columns: repeat(var(--count), 1fr);
        align-items: center;
        padding: 4px;
        border-radius: 0.5rem;
        position: relative;
        z-index: 0;
    }
    .selector::before {
        content: "";
        position: absolute;
        top: 4px;
        bottom: 4px;
        left: 4px;

        width: calc((100% - 8px) / var(--count));

        background-color: color-mix(in srgb, white 15%, transparent);
        border-radius: 0.3rem;

        transition: transform 0.25s cubic-bezier(0.4, 0, 0.2, 1);
        transform: translateX(calc(100% * var(--active)));
        z-index: -1;
    }
    .selector > button {
        background: transparent;
        border: none;
        font-family: inherit;
        line-height: 1;
        outline: none;
        margin: 0;

        flex: 1;
        text-align: center;

        padding: 6px 12px;
        font-size: 0.9rem;
        font-weight: 500;

        color: color-mix(in srgb, white 60%, transparent);
        border-radius: 0.3rem;
        cursor: pointer;
        transition: color 0.2s ease;
    }
    .selector > button:hover {
        color: white;
    }
    .selector > button.selected {
        color: white;
        font-weight: 600;
    }
    .title {
        align-self: center;
        justify-self: center;
    }
    table {
        margin: 0 auto 5px auto;
        border-spacing: 0;
    }
    .outer-wrapper {
        overflow: visible;
    }
    .scroll-wrapper {
        overflow: auto;
        max-height: 81.5vh;
        width: 100%;
        padding-bottom: 4.9px;
        padding-right: 4.8px;
    }
    th,
    td {
        padding: 1px 1px;
        text-align: center;
        vertical-align: middle;
        background-color: #14141a;
    }
    thead {
        position: sticky;
        top: 0;
        z-index: 10;
    }
    .rank,
    .number,
    .driver,
    .team,
    .avg {
        position: sticky;
    }
    .box {
        font-weight: 800;
        border-radius: 0.25rem;
        width: 1.5rem;
        padding: 0.08rem 0.1rem 0.08rem 0;

        display: flex;
        justify-content: center;
        align-items: center;

        aspect-ratio: 1/1;
        margin: 0 auto;
        color: #14141a;
        font-style: italic;
    }
    .rank {
        left: 0;
        width: 45px;
        min-width: 45px;
        max-width: 45px;
    }
    .number {
        left: 45px;
        width: 50px;
        min-width: 50px;
        max-width: 50px;
    }
    .driver {
        left: 95px;
        width: 120px;
        min-width: 120px;
        max-width: 120px;
        white-space: nowrap;
        overflow: hidden;
        text-overflow: ellipsis;
        font-weight: 600;
    }
    .team {
        left: 215px;
        width: 90px;
        min-width: 90px;
        max-width: 90px;
    }
    .avg {
        left: 305px;
        width: 60px;
        min-width: 60px;
        max-width: 60px;
    }
    .team-box {
        font-weight: 900;
        border-radius: 0.25rem;
        padding: 0.15rem 0;
        display: flex;
        justify-content: center;
        align-items: center;
        width: 4.5rem;
        margin: 0 auto;
        color: #14141a;
        font-size: 0.85rem;
    }
    thead .rank,
    thead .number,
    thead .driver,
    thead .team,
    thead .avg {
        z-index: 15;
    }
    td.rank,
    td.number,
    td.driver,
    td.team,
    td.avg {
        z-index: 5;
    }
</style>
