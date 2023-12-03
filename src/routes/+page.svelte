<script>
	import { fade } from "svelte/transition";
	import { onMount } from "svelte";
	import Circle from "../lib/components/background/Circle.svelte";

    let ready = false;

    onMount(() => {
        ready = true;
    })
</script>

<style>
    h1 {
        filter: drop-shadow(0 1px 3px);
    }

    .layers::before, .layers::after {
        content: attr(data-text);
        position: absolute;
        width: 100%;
    }

    .layers::before {
        top: -3px;
        left: -3px;
        z-index: 1;
        color: #B4B4B4;
        mix-blend-mode: lighten;
        display: none;
    }

    .layers::after {
        top: 3px;
        left: 5px;
        z-index: -1;
        color: #000000;
        mix-blend-mode: lighten;
    }

    .glitch {
        mix-blend-mode: lighten;
    }
</style>

<svelte:head>
    <meta name="description" content="Shadowgraph redefines online identity management through a personal, cryptographic, and private environment. By unifying your digital persona scattered across various online channels, Shadowgraph offers advanced data analytics and self-sovereign identity management via a dPKI-WoT and blockchain-agnostic framework. Embrace a future where you control and monetize your data through zero-knowledge proof technology and social diffusion ML modeling, ensuring privacy and accuracy in a hyper-personalized digital space.">
    <meta property="og:title" content="Shadowgraph - Your Digital Persona Unified" />
    <meta property="og:description" content="Shadowgraph is revolutionizing online interaction by offering a unified, cryptographic, and private platform for digital identity management. Our advanced approach empowers users with self-sovereign identity, leveraging dPKI-WoT and blockchain-agnostic systems to ensure unparalleled privacy and control. Join us in embracing a future where data is not just protected but harnessed for personal empowerment and monetization through cutting-edge zero-knowledge proof and ML social diffusion modeling.">
    <meta property="og:image" content="https://shadowgraph.ai/img/Shadowgraph_logo.webp">
    <meta property="og:url" content="https://shadowgraph.ai/">
</svelte:head>


<section class="flex min-h-full w-full text-white m-auto bg-[#00000000]">
    <div class="h-full w-full m-auto flex min-h-[600px]">
        <div class="absolute left-0 min-h-[600px] h-full w-full overflow-hidden pointer-events-all">
            <Circle />
        </div>
        <div class="flex flex-col full-height w-full text-center z-[2] mx-auto pointer-events-none">
            {#if ready}
                <div class="flex flex-col my-auto" in:fade={{ delay: 200, duration: 800 }}>
                    <div class="text-7xl">
                        <h1 class="glitch layers text-white/80 max-w-[420px] sm:max-w-[600px] md:max-w-[700px] mx-auto" data-text=""></h1>
                    </div>
                </div>
            {/if}
        </div>
    </div>
</section>
<!-- 
<section class="space-y-12 mt-16">
    <div class="text-white text-lg w-full md:w-1/2">
        <h2 class="text-2xl font-bold mb-2 monument-extended">Shadowgraph copy 1</h2>
        <p>Our philosophy of decentralisation leads us to take a bottom up approach to architecture reflects our commitment to building a more equitable and just future, where decentralized identity and trust systems provide the tools and resources needed for individuals and communities to take control of their digital lives. We believe that everyone has the right to privacy, security, and self-determination, and that decentralized systems are the key to unlocking that potential. Join us on our mission to build a self sovereign future for all.</p>
    </div>

    <div class="text-white text-lg md:text-right w-full md:w-1/2 md:ml-auto">
        <h2 class="text-2xl font-bold mb-2 monument-extended">Shadowgraph copy 2</h2>
        <p>At BraveNewCapital, we're not just building technology, we're building a movement. Our belief that decentralized identity and trust systems are the key to a more free and prosperous future. Join us on our mission to empower individuals and cooperates alike with a self-sovereign approach to identity, trust & reputation.<br/>Contact us today to learn more about how you can get involved. Trust the decentralized future with BraveNewCapital.</p>
    </div>
</section> -->