<script>
    import { Hamburger } from 'svelte-hamburgers';
	import MobileNav from './MobileNav.svelte';
	import { fade } from 'svelte/transition';
    import { disableBodyScroll, enableBodyScroll } from 'body-scroll-lock';

    let open;
    let navbar;

    let toggle = () => {
        open = !open;
    }

    $: open, navbar ? open ? disableBodyScroll(navbar) : enableBodyScroll(navbar) : null;
</script>

<svelte:window on:resize={() => open = false} />

<nav bind:this={navbar} class="fixed top-0 left-0 right-0 mx-auto z-[12] backdrop-blur text-white border-b-[1px] border-gray-700" in:fade={{ delay: 200, duration: 300 }}>
    <div class="flex text-md px-2 space-x-6 w-full max-w-[1280px] mx-auto">
        <a class="flex flex-col font-bold text-lg my-auto" href="/">
            <h2 class="tracking-widest p-2">Shadowgraph</h2>
        </a>
        <div class="flex justify-end my-auto w-full">
            <div class="hidden sm:block space-x-6 ml-8 h-[60px]">
                <a class="underline-offset-8 decoration-2 hover:underline" href="/">
                    <button class="h-full border-t-2 border-transparent hover:border-[#B4B4B4] w-[80px] transition ease-in-out duration-400">Protocol</button>
                </a>
                <a class="underline-offset-8 decoration-2 hover:underline" href="/blog">
                    <button class="h-full border-t-2 border-transparent hover:border-[#B4B4B4] w-[80px] transition ease-in-out duration-400">Vision</button>
                </a>
                <!-- <a class="underline-offset-8 decoration-2 hover:underline" href="/mission">
                    <button class="h-full border-t-2 border-transparent hover:border-[#B4B4B4] w-[80px] transition ease-in-out duration-400">Launch Application</button>
                </a> -->
            </div>
        </div>
        <div class="flex">
            <button class="border-2 rounded-lg ml-auto px-3 h-[40px] hidden sm:block my-auto hover:bg-white w-[150px] hover:text-black">Try the App</button>
            <div class="sm:hidden my-auto pr-2">
                <Hamburger bind:open --color="white" --padding={"0px"} />
            </div>
        </div>
    </div>
</nav>

{#if open}
    <MobileNav toggle={toggle} />
{/if}