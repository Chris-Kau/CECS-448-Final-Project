<script>
    import { createEventDispatcher } from 'svelte';
    const dispatch = createEventDispatcher();

    export let isDarkMode;

    function toggleTheme() {
        dispatch('toggleTheme');
    }
</script>

<header class="fixed top-0 left-0 right-0 h-16 {isDarkMode?  "bg-sky-800" : "bg-[#66ccff]"} 
    shadow-md flex items-center justify-between px-6 z-10 transition-colors duration-300 border-b-2">
    
    <h1 class="text-xl font-semibold text-cs-primary dark:text-cs-secondary">Canvas Board</h1>
    
    <button 
        on:click={toggleTheme} 
        class="p-2 rounded-full bg-gray-200 dark:bg-gray-700 
            text-gray-800 dark:text-gray-200 transition-colors duration-300 hover:scale-110 cursor-pointer"
        aria-label="Toggle Dark Mode"
    >
    <span aria-hidden="true">
        {#if isDarkMode}
            🌙
        {:else}
            ☀️
        {/if}
    </span>
    </button>
</header>