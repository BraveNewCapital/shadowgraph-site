<script>
	import { fade, slide } from "svelte/transition";

    export let toggle;
</script>

<div class="fixed left-0 top-0 full-height w-screen bg-black/90 z-[10] backdrop-blur" in:fade={{ duration: 200 }} out:fade>
    <div class="text-white h-full flex flex-col text-4xl text-center" transition:slide|local>
        <div class="flex flex-col m-auto space-y-8">
            <a href="/" on:click={toggle}>Protocol</a>
            <a href="/blog" on:click={toggle}>Vision</a>
            <hr />
            <button class="border-2 rounded-lg h-[45px] text-sm my-auto hover:bg-white hover:text-black">Try the App</button>

            <!--<button class="" on:click={toggle}>Connect</button>-->
        </div>
    </div>
</div>