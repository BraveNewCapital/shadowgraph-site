<script>
	import { fade } from "svelte/transition";
	import { onMount } from "svelte";
	import Circle from "../lib/components/background/Circle.svelte";
    import TypewriterInput from '../lib/components/Typewriter.svelte';

    let ready = false;
    let buttonText = 'GET'; // Initial text for the button

    // Function to revert text when not hovering
    function revertText() {
        buttonText = 'GET';
    }
    // Function to change text on hover
    function changeTextOnHover() {
        buttonText = 'NOW';
    }


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
    <meta property="og:title" content="Shadowgraph - Self-Sovereign Unified Digital Persona" />
    <meta property="og:description" content="Shadowgraph is changing the way we think about interacting online. By offering a unified, cryptographic, private & market driven platform for digital identity, reputation, trust and metrics.     Our approach via self-sovereign identity, leveraging dPKI-WoT with DIDs and blockchain-agnostic dpki ledger systems to scalably ensure privacy and control. Embracing a user centric future, where data is not rendundantly or perversely used by centeralised entities. Data, all of it - harnessed for personal privacy, empowerment and monetization (via ZKML 🪄).">
    <meta property="og:image" content="https://shadowgraph.ai/img/Shadowgraph_logo.webp">
    <meta property="og:url" content="https://shadowgraph.ai/">
</svelte:head>

<section class="flex h-full min-h-[600px] max-h-[100vh] w-full text-white m-0 bg-[#00000000]">
    <div class="h-full w-full m-auto flex min-h-[600px]">
        <div class="absolute left-0 min-h-[600px] h-full w-full overflow-hidden pointer-events-all">
            <Circle />
        </div>
        <div class="flex flex-col full-height w-full text-center z-[2] mx-auto">
            {#if ready}
                <div class="flex flex-col my-auto" in:fade={{ delay: 200, duration: 800 }}>
                    <form class="max-w-md max-h-fit w-full mx-auto">
                        <div class="flex items-center border-b border-b-2 border-white py-2">
                            <input id="typewriter-input" class="appearance-none bg-transparent border-none w-full text-gray-300 mr-3 py-1 px-2 pl-0 leading-tight focus:outline-none"  type="email"/>
                            <TypewriterInput 
                            inputPlaceholderId="typewriter-input" 
                            texts = {[
                                'satoshi@visionmail.com', // Email Address as a Unique Identifier
                                'BTC/ETH Address', // Bitcoin/Ethereum Addresses for Cryptocurrency Transactions and Identity
                                'Medium Profile', // Medium Writing Profile for Content Creator Identity
                                'MoonPay Passport',
                                'Patreon Profile', // Patreon Content Creator Account for Digital Identity in Arts and Media
                                'Twitch Channel', // Twitch Streaming Channel for Gamer and Streamer Identity
                                'Mastodon Account', // Mastodon Account for Decentralized Social Networking Identity
                                'Substrate Identity', // Substrate-Based Blockchain Identity
                                'ENS Name', // Ethereum Name Service for Human-Readable Ethereum Addresses
                                'uPort ID', // uPort Identifier for Self-Sovereign Identity on Ethereum
                                'Keybase Username', // Keybase.io for Identity Verification across Platforms
                                'Hyperledger Indy DID', // Hyperledger Indy Decentralized Identifier for Identity Solutions
                                'nostr ID', // Nostr Protocol Identifier for Decentralized Social Networking
                                'ERC-725 Identity Contract', // Ethereum ERC-725 Standard for Blockchain-Based Identity
                                'TOR_@_Address.Onion', // TOR Network Address for Anonymous Identity Verification
                                'Facebook Profile', //Facebook lol
                                'Matrix ID', // Matrix Protocol Identifier for Secure, Decentralized Communication
                                'DID Identifier', // Decentralized Identifier for Self-Sovereign Identity
                                'OpenPGP Key', // OpenPGP Key for Secure Communications and Identity Verification
                                'Signal Number', // Signal App Number for Secure Messaging and Identity Verification
                                'Telegram @Handle', // Telegram Username for Secure Communication and Identity
                                'Discord ID', // Discord ID for Identity in Gaming and Tech Communities
                                'GitHub Account', // GitHub Username for Identity in Software Development
                                'Soulbound DID Token', // Ethereum Soulbound Token for Non-Transferable Digital Identity
                                'LinkedIn Profile', // LinkedIn URL for Professional Identity
                                'Monero Address LOL', // Monero Address for Privacy-Focused Digital Identity
                                'Polkadot Address', // Polkadot Address for Interoperable Blockchain Identity
                                'GPG Fingerprint', // GPG Key Fingerprint for Secure Communication and Identity Verification
                                'BEGIN PGP Public Key...', // Public Key for PGP Encryption and Identity Verification
                                'EOS Account', // EOS Account for Decentralized Autonomous Organizations Identity
                                'Tezos Address', // Tezos Address for Decentralized Identity in Smart Contracts
                                'Civic ID', // Civic Identity for Secure and Private Digital Identity Verification
                                'Ontology ID', // Ontology Blockchain Identifier for Decentralized Identity
                                'SelfKey ID', // SelfKey Identifier for Self-Sovereign Identity Management
                                'Handshake Name', // Handshake Decentralized Naming Protocol Identifier
                                'someone@ProtonMail.com', // ProtonMail Address for Secure Email-Based Identity
                                'Google ID' ]
                            }
                            typingSpeed={177} 
                            pauseDuration={1618} 
                            shuffle={true} />
                            
                            <!-- svelte-ignore a11y-mouse-events-have-key-events -->
                            <button class="border-2 rounded-xl ml-auto px-10 h-[35px] my-auto hover:bg-white hover:text-black focus:outline-none focus:shadow-outline"
                                type="submit" title="Shadowgraph metasocial-network" 
                                on:mouseover={changeTextOnHover} 
                                on:mouseout={revertText}>
                                {buttonText}
                            </button>
                        </div>
                    </form>
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