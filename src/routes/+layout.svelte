<script lang="ts">
    import '../app.postcss';
	import { onMount } from "svelte";
    import { AppBar, LightSwitch } from '@skeletonlabs/skeleton';
	import { persisted } from "svelte-persisted-store";

    const currentTheme = persisted<string>('currentTheme','');
	const loadThemeState = (): void => {
		const savedTheme = localStorage.getItem("currentTheme");
		if(savedTheme) {
			const loadedTheme: string = JSON.parse(savedTheme);
			currentTheme.set(loadedTheme);
			document.body.setAttribute("data-theme", loadedTheme);
		} else {
			currentTheme.set("vintage");
		}
	};
	const themes: string[] = [
		'skeleton',
		// 'wintry',
		'modern',
		'rocket',
		'seafoam',
		'vintage',
		// 'sahara',
		// 'hamlindigo',
		'gold-nouveau',
		'crimson',
	];
	let currentIndex: number;
	let nextIndex: number;

	const changeTheme = (): void => {
		currentIndex = themes.indexOf($currentTheme);
		nextIndex = (currentIndex + 1) % themes.length;
		currentTheme.set(themes[nextIndex]);
		document.body.setAttribute("data-theme", themes[nextIndex]); 
	};
	onMount(loadThemeState);
</script>
        <!-- App Bar -->
        <AppBar>
            <svelte:fragment slot="lead">
                <strong class="text-xl">Jon-no-H Bingo Board</strong>
            </svelte:fragment>
            <svelte:fragment slot="trail">
                <button class="btn btn-sm variant-glass-surface" on:click={changeTheme}>
					<!-- {$currentTheme} -->
					 theme
				</button>
                <LightSwitch />
            </svelte:fragment>
        </AppBar>
    <!-- Page Route Content -->
<slot />
