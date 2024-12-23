<script lang="ts">
    import '../app.postcss';
    import { AppBar, LightSwitch } from '@skeletonlabs/skeleton';
</script>
        <!-- App Bar -->
        <AppBar>
            <svelte:fragment slot="lead">
                <strong class="text-xl">Jon-no-H Bingo Board</strong>
            </svelte:fragment>
            <svelte:fragment slot="trail">
                <LightSwitch />
            </svelte:fragment>
        </AppBar>
    <!-- Page Route Content -->
<slot />
