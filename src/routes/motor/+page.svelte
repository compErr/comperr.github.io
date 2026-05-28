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
    <div class="table">
        <!-- <div class="all-countries">
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
        </div> -->
        <div class="fixed">
            <div class="cell topgap"></div>
            <div class="cell topgap"></div>
            <div class="cell topgap"></div>
            <div class="cell topgap"></div>
            <div class="cell topgap"></div>
            <div class="cell title">RANK</div>
            <div class="cell title">NO</div>
            <div class="cell title">DRIVER</div>
            <div class="cell title">TEAM</div>
            <div class="cell title">AVG</div>
            {#each rows.slice(1) as row, i}
                {@const name = row["DRIVER"].split(" ")}
                <div class="cell rank">{i + 1}</div>
                <div class="cell number">
                    <div class="box">{row["NO"]}</div>
                </div>
                <div class="cell driver">{`${name[0][0]}.${name[1]}`}</div>
                <div class="cell team">
                    {row["TEAM"] == "Racing Bulls"
                        ? "VCARB"
                        : row["TEAM"] == "Aston Martin"
                          ? "AMR"
                          : row["TEAM"]}
                </div>
                <div class="cell avg"><Badge rating={row["AVG"]}></Badge></div>
            {/each}
        </div>
        <div class="points"></div>
    </div>
{/if}

<style>
    .table {
        display: grid;
        grid-template-columns: 0.4fr 1fr;
    }
    .fixed {
        display: grid;
        width: 100%;
        /*grid-template-rows: repeat(10, 1fr);*/
        grid-template-columns: 0.3fr 0.3fr 1fr 0.7fr 0.4fr;
        grid-auto-rows: 0.9fr;
        /*position: sticky;*/
    }
    .title {
        font-weight: 900;
    }
    .box {
        background-color: red;
        height: 100%;
        aspect-ratio: 1/1;
        display: flex;
        align-items: center;
        justify-content: center;
        border-radius: 0.2rem;
    }
    .cell {
        background-color: black;
        padding: 1px;
        display: flex;
        align-items: center;
        justify-content: center;
        margin: 0.1rem;
    }
    .number {
        font-weight: 900;
    }
    .team {
        word-spacing: -7px;
    }
    /*.col {
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
    }*/

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
