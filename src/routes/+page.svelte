<script lang="ts">
    import { LucideDownload, LucideChevronRight } from "@lucide/svelte";
    import Dropdown from "$lib/components/dropdown.svelte";
    import { AboutMe, Contact, Work } from "$lib/components/content";
</script>

<h1>Joseph Jitto</h1>
<h4>Newly Graduated Dev</h4>
<div class="codeBlock">
    <div class="file">
        <p>Portfolio.code</p>
        <div class="cv-link">
            <span>CV Download Link --&gt;</span>
            <LucideDownload size={18}></LucideDownload>
        </div>
    </div>
    <div class="code">
        <div class="line">
            <span class="burple">type</span>
            <span class="aqua">Portfolio</span>
            <span class="burple">struct</span>
            <span class="bracket">&lcub;</span>
        </div>

        <div class="indent">
            <Dropdown name={"AboutMe"} content={AboutMe}></Dropdown>
            <Dropdown name={"Experience"} content={Work}></Dropdown>
            <Dropdown name={"Projects"}></Dropdown>
            <Dropdown name={"ContactDetails"} content={Contact}></Dropdown>
            <Dropdown name={"Posts"}></Dropdown>
            <Dropdown name={"Hobbies"}></Dropdown>
        </div>
        <div class="line bracket">&rcub;</div>
    </div>
</div>

<style>
    h1 {
        font-weight: 800;
        margin-bottom: 0;
        color: whitesmoke;
    }
    h4 {
        font-weight: 500;
        margin-top: 0;
        color: darkgrey;
    }
    .aqua {
        color: #16b9d7;
    }
    .burple {
        color: #bb9af7;
    }
    .bracket {
        color: #89ddff;
    }
    .cv-link {
        display: flex;
        align-items: center;
        gap: 8px; /* Gives a clean spacing between the arrow text and the icon */
    }
    .codeBlock {
        border-radius: 16px;
        background-color: #1a1b26;
        overflow: hidden;
        box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3);
    }
    .file {
        display: flex;
        align-items: center;
        justify-content: space-between;
        font-size: 14px;
        border-bottom: 2px solid #2a2c3b;
        padding: 10px 24px;
        color: #a9b1d6;
        font-weight: 700;
    }
    .file p {
        margin: 0;
    }
    .code {
        font-family: "Consolas", "Fira Code", "JetBrains Mono", monospace;
        padding-top: 16px;
        padding-bottom: 16px;
        font-size: 18px;
        color: #c0caf5;
        line-height: 20px;
    }
    .line {
        display: flex;
        gap: 1ch;
        padding-left: 24px;
        padding-right: 24px;
    }
    .indent {
        padding-right: 1rem;
    }
</style>
