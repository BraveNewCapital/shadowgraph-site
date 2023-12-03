<script>
	import '../app.postcss';
	import Navbar from "$lib/components/navigation/Navbar.svelte";
    import currentYear from "current-year";
	import { onMount } from 'svelte';

	let ready = false;

    onMount(() => {
        ready = true;
    });
</script>

<main class="flex flex-col h-full max-w-[1280px] m-auto px-6 min-h-screen">
    {#if ready}
        <Navbar />
    {/if}

	<slot />

    <footer class="flex bottom-0">
        <div class="flex flex-col mt-16 mb-12 m-auto">
            <p class="text-xs text-gray-500 m-auto">© {currentYear()} Shadowgraph a <a class="underline-offset-8 decoration-2 hover:underline" href="https://BraveNew.Capital">Brave New Capital</a> venture</p>
        </div>
    </footer>
</main>