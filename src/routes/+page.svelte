<script>
    import Footer from "$lib/components/Footer.svelte";
    import { name } from "$lib/utils/stors.js";
    import {fade} from "svelte/transition";
    let nameValue = '';
    $: name.subscribe(value => {
        nameValue = value;
    });
    $: if (nameValue !== $name) {
        name.set(nameValue);
    }
    let animeVisible = false;

    function onVideoEnd() {
        animeVisible = true;
        // You can perform other actions here
    }
</script>

{#if !animeVisible}
    <video width="750" height="500" controls on:ended={onVideoEnd} autoplay>
        <source src="/animLogo.mp4" type="video/mp4">
    </video>
{:else}
    <div transition:fade={{ delay: 250, duration: 300 }} class="bg-[#F7FDFF] w-screen h-screen flex flex-col items-center justify-center gap-20">
        <img src="/img.png" alt="logo" class="w-40 absolute top-20 left-0 right-0 mx-auto" />
        <div class="w-72 gap-5 flex flex-col items-center justify-center">
            <p class="text-2xl text-center text-black">
                Quel nom souhaitez-vous donner à votre goutte ?
            </p>
            <div class="relative w-full min-w-[200px] h-10">
                <input bind:value={nameValue} type="text"
                        class="w-full h-full bg-transparent text-blue-gray-700 font-sans font-normal outline px-3 rounded-2xl focus:outline-none focus:ring-2 focus:ring-red-400 focus:border-transparent"
                        placeholder="Nom" />
            </div>
            <a href="/GameMenu" class="rounded-3xl text-center bg-blue-600 text-white w-40 py-6">
                Commencez !
            </a>
        </div>
    </div>
    <Footer />
{/if}