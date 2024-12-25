<script lang="ts">
    import { onMount } from "svelte";
    import { persisted } from "svelte-persisted-store";
    import { stuffdadsays, type Cell } from "$lib/index";

    const pickedPhrases = persisted<string[]>('pickedPhrases', []);

    function pickPhrase(): void {
        if (stuffdadsays.length === 0 || $pickedPhrases.length === stuffdadsays.length) return;

        let pickedPhrase: string;
        do {
            const randomIndex = Math.floor(Math.random() * stuffdadsays.length);
            pickedPhrase = stuffdadsays[randomIndex];
        } while ($pickedPhrases.includes(pickedPhrase));
        pickedPhrases.update((phrases) => [pickedPhrase, ...phrases]);
    }

    function clearPhrases(): void {
        pickedPhrases.set([]);
        pickPhrase();
    }

    onMount(() => {
        pickPhrase(); // Pick an initial phrase on mount
    });

    $: isOutOfPhrases = $pickedPhrases.length === stuffdadsays.length;
</script>


<div class="container h-full mx-auto flex justify-center items-center">
    <div class="space-y-10 text-center flex flex-col items-center">
        <button class="btn btn-xl variant-ghost-primary" on:click={pickPhrase} disabled={isOutOfPhrases}>Pick a Phrase</button>
        <div class="space-y-3">
            {#each $pickedPhrases as phrase}
            <!-- svelte-ignore a11y-click-events-have-key-events -->
            <!-- svelte-ignore a11y-no-static-element-interactions -->
            <div class="card break-words whitespace-normal variant-ghost-primary">
                <section class="p-2">{phrase}</section>
            </div>
            {/each}
        </div>
        <button class="btn btn-xl variant-ghost-warning" on:click={clearPhrases}>Reset</button>
    </div>
</div>
