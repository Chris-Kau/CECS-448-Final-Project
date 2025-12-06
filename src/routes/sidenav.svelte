<script>
    import { createEventDispatcher } from "svelte";
    import { fly } from "svelte/transition";
    import "../app.css";
    
    let loggedIn = false;
    let isTokenInfoOpen = false;
    export let token = "";
    export let isDarkMode;

</script>

<div
    class="fixed top-16 left-0 h-full w-[15%] z-20
    {isDarkMode
        ? 'bg-[#101828]'
        : 'bg-white'} p-2 flex flex-col items-center shadow-lg
    transition-colors duration-300 border-r-2"
    style="height: calc(100vh - 4rem);"
>
    <button
        class="w-[80%] py-2 mb-3 rounded-md {!isDarkMode
            ? 'bg-[#66CCFF]'
            : 'bg-[#00598A]'}
            text-cs-primary text-base font-medium
            transition duration-200 hover:scale-[1.05] border border-cs-todo-border
            dark:border-gray-600 hover:bg-gray-400"
    >
        + Create Task
    </button>

    <button
        class="w-[80%] py-2 rounded-md {!isDarkMode
            ? 'bg-[#66CCFF]'
            : 'bg-[#00598A]'}
            text-cs-primary text-base font-medium
            transition duration-200 hover:scale-[1.05] border border-cs-todo-border
            dark:border-gray-600 hover:bg-gray-400"
    >
        + Create Note
    </button>

    <div class="grow"></div>

    <div class="flex justify-center w-[95%] relative mb-2">
        <input
            type="password"
            bind:value={token}
            placeholder="Insert Canvas Token Here"
            class="w-[80%] my-1 p-1 text-sm rounded-md border-0
                {!isDarkMode
                ? 'bg-[#66CCFF] text-gray-800'
                : 'bg-[#00598A] text-gray-200'}"
        />
        <button
            class="ml-3 hover:scale-[1.2]"
            on:click={() => (isTokenInfoOpen = !isTokenInfoOpen)}
        >
            ℹ️
        </button>
        {#if isTokenInfoOpen}
            <div
                class="absolute 
                    top-1/2 left-full transform -translate-y-1/2 ml-2
                    w-64 p-3 rounded-lg shadow-xl text-xs z-30
                    {!isDarkMode
                        ? 'bg-white text-gray-800 border border-gray-200'
                        : 'bg-gray-700 text-gray-100 border border-gray-600'}"
                transition:fly={{ x: 5, duration: 150 }}
            >
                <p class="font-semibold mb-1">How do you get this token?</p>
                <p>
                    To retrieve your canvas api token, log into your canvas
                    account, navigate to account, then settings, then click on +
                    New Access Token
                </p>

                <div
                    class="absolute -left-1.5 top-1/2 transform -translate-y-1/2 w-0 h-0 
                        border-y-8 border-y-transparent border-r-8
                        {!isDarkMode ? ' border-r-white' : ' border-r-gray-700'}"
                ></div>
            </div>
        {/if}
    </div>

    {#if loggedIn}
        <div>
            <p>Logged in as: John Doe</p>
        </div>
    {/if}
    <button
        class="w-[80%] mx-1 py-1 mb-5 rounded-md 
            transition duration-200 hover:scale-[1.05] border border-cs-todo-border {!isDarkMode
            ? 'bg-[#66CCFF] text-black'
            : 'bg-[#00598A] text-white'}"
    >
        IMPORT CANVAS
    </button>
</div>
