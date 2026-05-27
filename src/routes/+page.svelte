<script lang="ts">
    import Dropdown from "$lib/components/dropdown.svelte";
    import { AboutMe, Contact, Work } from "$lib/components/content";
    import H from "$lib/assets/tyres/F1_tire_Pirelli_PZero_Purple 1.png";
    import M from "$lib/assets/tyres/F1_tire_Pirelli_PZero_Purple 2.png";
    import S from "$lib/assets/tyres/F1_tire_Pirelli_PZero_Purple 3.png";
    import SS from "$lib/assets/tyres/F1_tire_Pirelli_PZero_Purple 4.png";
    import { LucideChevronRight, LucideChevronLeft } from "@lucide/svelte";
    const icon = {
        0: SS,
        1: S,
        2: M,
        3: H,
    };

    let st = $state(1);
    //0 SS : Simple
    //1 S: Reg
    //2 M: Bright
    //3 H: Flashbang
</script>

<div class="top">
    <div>
        <div class="hover-zone">
            <h1 class="title">Joseph Jitto</h1>
            <h1 class="hidden">
                <span class="gradient-text">ജോസഫ് ജിറ്റോ</span>
            </h1>
        </div>
        <h4>Newly Graduated Dev</h4>
    </div>
    <div class="inline-style">
        <button
            class="negative-btn"
            aria-label="Next theme"
            onclick={() => {
                st = st - 1;
                if (st < 0) st = 0;
            }}
        >
            <LucideChevronLeft size={16} strokeWidth={5} />
        </button>
        <img src={icon[st as keyof typeof icon]} class="logo" alt="baground" />
        <button
            class="negative-btn"
            aria-label="Next theme"
            onclick={() => {
                st = st + 1;
                if (st > 3) st = 3;
            }}
        >
            <LucideChevronRight size={16} strokeWidth={5} />
        </button>
    </div>
</div>
<div class="codeBlock">
    <div class="file">
        <p>Portfolio.code</p>
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
            <Dropdown name={"Hobbies"}></Dropdown>
            <Dropdown name={"Posts"}></Dropdown>
        </div>
        <div class="line bracket">&rcub;</div>
    </div>
</div>

<style>
    .inline-style {
        display: flex;
        align-items: center;
        gap: 12px;
        display: none;
    }
    .negative-btn {
        display: flex;
        align-items: center;
        justify-content: center;
        width: 28px;
        height: 28px;
        border: none;
        border-radius: 0.25rem;
        cursor: pointer;
        background-color: #232433;
        color: #14141a;
        transition:
            transform 0.2s ease,
            background-color 0.2s ease;
    }
    .negative-btn:hover {
        transform: scale(1.05);
        background-color: #a9b1d6;
    }
    .negative-btn:active {
        transform: scale(0.95);
    }
    .logo {
        width: 35px;
        object-fit: contain;
    }
    .top {
        display: flex;
        justify-content: space-between;
        padding-top: 25px;
    }
    .title {
        margin: 0;
        line-height: 0.8;
        position: relative;
        z-index: 2;
    }
    .hidden {
        max-height: 0;
        overflow: hidden;
        margin: 0;
        line-height: 0.8;
        margin-top: 1px;
        transition: max-height 0.4s cubic-bezier(0.2, 0.8, 0.2, 1);
        position: relative;
        z-index: 1;
    }
    .hover-zone:hover .hidden {
        max-height: 100px;
    }
    .gradient-text {
        display: inline-block;
        font-size: 1.8rem;
        font-weight: 800;
        transform-origin: top left;

        opacity: 0;
        transition: all 0.4s cubic-bezier(0.2, 0.8, 0.2, 1);
        transform: skew(35deg, 0deg) scaleX(0.84) scaleY(0) translateX(-1px);

        background-image: linear-gradient(
            to bottom,
            transparent 0%,
            darkgrey 100%
        );
        -webkit-background-clip: text;
        -webkit-text-fill-color: transparent;
    }
    .hover-zone:hover .gradient-text {
        transform: skew(35deg, 0deg) scaleX(0.84) scaleY(1) translateX(-1px);
        opacity: 1;
    }
    .hover-zone {
        padding-bottom: 10px;
    }
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
