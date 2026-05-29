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
    let showOnlyAvg = $state(false);
    let showMode = $state(1);

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

            keys = Object.keys(rows[1]);
            headers = Object.keys(rows[0]).filter(
                (key) => !key.includes("__EMPTY"),
            );
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
    <button class="toggle-btn" onclick={() => (showOnlyAvg = !showOnlyAvg)}>
        {showOnlyAvg ? "Show All Sessions" : "Show Only Averages"}
    </button>
    <div class="outer-wrapper">
        <div class="scroll-wrapper">
            <table>
                <thead>
                    <tr>
                        <th rowspan="2" class="rank">RANK</th>
                        <th rowspan="2 " class="number">NO</th>
                        <th rowspan="2" class="driver">DRIVER</th>
                        <th rowspan="2" class="team">TEAM</th>
                        <th rowspan="2" class="avg">AVG</th>

                        {#each headers as header}
                            {@const isSprint = rows[0][header] === "SQ"}
                            <th
                                colspan={showOnlyAvg ? 1 : isSprint ? 5 : 3}
                                class="race-name"
                            >
                                {header}
                            </th>
                        {/each}
                    </tr>

                    <tr>
                        {#if !showOnlyAvg}
                            {#each headers as header}
                                {@const isSprint = rows[0][header] === "SQ"}
                                {#each isSprint ? ["SQ", "SR", "Q", "R", "AVG"] : ["Q", "R", "AVG"] as sub}
                                    <th class="sub-header">{sub}</th>
                                {/each}
                            {/each}
                        {/if}
                    </tr>
                </thead>
                <tbody>
                    {#each rows.slice(1) as row, i}
                        {@const name = row["DRIVER"].split(" ")}
                        <tr>
                            <td class="rank">{i + 1}</td>
                            <td class="number"
                                ><div class="box">{row["NO"]}</div></td
                            >
                            <td class="driver">{`${name[0][0]}.${name[1]}`}</td>
                            <td class="team">
                                {row["TEAM"] === "Racing Bulls"
                                    ? "VCARB"
                                    : row["TEAM"] === "Aston Martin"
                                      ? "AMR"
                                      : row["TEAM"]}
                            </td>
                            <td class="avg"
                                ><Badge rating={row["AVG"].toFixed(2)} /></td
                            >

                            {#each keys.slice(5) as key, idx}
                                {@const nxtKey = keys.slice(5)[idx + 1]}
                                {@const isAvg =
                                    !nxtKey || !nxtKey.includes("__EMPTY")}

                                {#if !showOnlyAvg || isAvg}
                                    <td class="badge-cell"
                                        ><Badge rating={row[key]}></Badge></td
                                    >
                                {/if}
                            {/each}
                        </tr>
                    {/each}
                </tbody>
            </table>
        </div>
    </div>
{/if}

<style>
    table {
        margin-bottom: 5px;
        border-spacing: 0;
    }
    .outer-wrapper {
        overflow: visible;
    }

    .scroll-wrapper {
        overflow: auto;
        max-height: 81.5vh;
        width: 100%;
        /* Adds a gap between the table and the scrollbars */
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

    .box {
        width: 2.4rem;
        margin: 0 auto;
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
