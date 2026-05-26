<script lang="ts">
    import Badge from "$lib/components/badge.svelte";
</script>

<div>
    <div>
        B.Tech graduate in CSE (AI &amp; ML) from VIT Chennai, actively seeking
        new opportunities. I&#39;m a versatile developer driven by a pure love
        for coding and learning.
    </div>
    <div class="badges">
        <Badge text="Location" color="grey" extraText="Dubai"></Badge>
        <Badge text="Degree" color="grey" extraText="B.Tech CSE spl. AI & ML"
        ></Badge>
        <Badge text="University" color="grey" extraText="VIT Chennai"></Badge>
    </div>
</div>

<style>
    .badges {
        padding-top: 0.25rem;
        display: flex;
        flex-wrap: wrap;
        gap: 0.5rem;
    }
</style>
