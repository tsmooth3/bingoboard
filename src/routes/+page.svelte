
<script lang="ts">
    import { onMount } from "svelte";
    import { persisted } from "svelte-persisted-store";
	import { stuffdadsays, type Cell } from "$lib/index";

    const board = persisted<Cell[]>("bingoBoard",[]);
    const bingoMessage= persisted<string>("bingoMessage","");
    const activeCard = persisted<boolean>("activeCard",false);
    const boardsize:number = 4;

    const generateBoard = (): void => {
        const selectedPhrases: string[] = [...stuffdadsays].sort(() => 0.5 - Math.random()).slice(0, boardsize ** 2);
        const newBoard: Cell[] = selectedPhrases.map((phrase, index) => ({
            id: index,
            text: phrase,
            highlighted: false,
        }));
        board.set(newBoard);
        bingoMessage.set("");
                activeCard.set(false);
        saveBoardState(newBoard);
    };


    const saveBoardState = (currentBoard: Cell[]): void => {
        localStorage.setItem("bingoBoard", JSON.stringify(currentBoard));
    };


    const loadBoardState = (): void => {
        const savedBoard = localStorage.getItem("bingoBoard");
        if(savedBoard) {
                    const loadedBoard: Cell[] = JSON.parse(savedBoard);
                    board.set(loadedBoard);
                    checkActive(loadedBoard);
        } else {
            generateBoard();
        }
    };


    const resetBoard = (): void => {
        localStorage.removeItem("bingoBoard");
        generateBoard();
    };
    const clearBoard = (): void => {
        board.update((b) => {
            b.some((cell) => cell.highlighted = false)
            saveBoardState(b);
            checkActive(b);
            return b;
        });
        checkBingo();
    };


    const toggleHighlight = (id: number): void => {
        board.update((b) => {
            const cell = b.find((c) => c.id === id);
            if (cell) {
                cell.highlighted = !cell.highlighted;
            }
            saveBoardState(b);
                        checkActive(b);
            return b;
        });
        checkBingo();
    };


    const checkActive = (currentBoard: Cell[]): void => {
        activeCard.set(currentBoard.some((cell) => cell.highlighted));
    };


    const checkBingo = (): void => {
        let isBingo = false;
		const currentTime = new Date();
		const formattedTime = currentTime.toISOString();
		
        board.update((b) => {
            //check rows
            for (let i=0; i< boardsize; i++) {
                if (b.slice(i * boardsize, (i+1) * boardsize).every((cell) => cell.highlighted)) {
                    isBingo = true;
                }
            }
            //check columns
            for (let i=0; i< boardsize; i++) {
                if (b.filter((_, index) => index % boardsize === i).every((cell) => cell.highlighted)) {
                    isBingo = true;
                }
            }
            //check diag
            const diagonal1 = b.filter((_, index) => index % (boardsize + 1) === 0);
            const diagonal2 = b.filter((_, index) => index % (boardsize - 1) === 0 && index > 0 && index < b.length - 1);
            if(diagonal1.every((cell) => cell.highlighted) || diagonal2.every((cell) => cell.highlighted)) {
                isBingo = true;
            }
            return b;
        });
        bingoMessage.set(isBingo ? "BINGO!" : "");
    };


    onMount(loadBoardState);
</script>


<div class="container h-full mx-auto flex justify-center items-center">
    <div class="space-y-10 text-center flex flex-col items-center">
        {#if $bingoMessage === ''}
            {#if $activeCard}
                <button type="button" class="btn btn-xl w-full variant-ghost-warning" on:click={clearBoard}>Clear Board</button>
            {:else}
                <button type="button" class="btn btn-xl w-full variant-ghost-error" on:click={resetBoard}>New Card</button>
            {/if}
        {:else}
            <button type="button" class="btn btn-xl w-full variant-ghost-success" on:click={clearBoard}>{$bingoMessage}</button>
        {/if}
        <div class="grid grid-cols-4 gap-3">
            {#each $board as cell}
                <!-- svelte-ignore a11y-click-events-have-key-events -->
                <!-- svelte-ignore a11y-no-static-element-interactions -->
                <div class="card break-words whitespace-normal {cell.highlighted ? 'variant-ghost-primary' : 'variant-ghost-tertiary'}" on:click={() => toggleHighlight(cell.id)}>
                        <section class="p-2">{cell.text}</section>
                </div>
            {/each}
        </div>
    </div>
</div>
