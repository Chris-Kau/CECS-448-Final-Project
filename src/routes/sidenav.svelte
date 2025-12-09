<script>
    import { createEventDispatcher } from "svelte";
    import { fly } from "svelte/transition";
    import "../app.css";
    import Stickynote from "./StickyNote.svelte";
    
    const dispatch = createEventDispatcher();
    let loggedIn = false;
    let isTokenInfoOpen = false;
    let newStickyNoteTitle = "";
    let isStickyNoteTitleEmpty = false;
    export let token = "";
    export let isDarkMode;

let isStickyNoteModalOpen = false;

    function openStickyNoteModal() {
        isStickyNoteModalOpen = true;
    }

    function closeStickyNoteModal() {
        isStickyNoteTitleEmpty = false;
        isStickyNoteModalOpen = false;
    }

    // Handlers for the events
    function handleImport() {
        loggedIn = true;
        dispatch("importCanvas");
    }

    function handleCreateStickyNote(color) {
        if (newStickyNoteTitle == ''){
            isStickyNoteTitleEmpty = true;
            return;
        }
        isStickyNoteTitleEmpty = false
        dispatch("createStickyNote", {
            title: newStickyNoteTitle || "New Sticky Note",
            color,
        });
        newStickyNoteTitle = "";
        closeStickyNoteModal();
    }
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
        on:click={openStickyNoteModal}
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
        on:click={handleImport}
        class="w-[80%] mx-1 py-1 mb-5 rounded-md 
            transition duration-200 hover:scale-[1.05] border border-cs-todo-border {!isDarkMode
            ? 'bg-[#66CCFF] text-black'
            : 'bg-[#00598A] text-white'}"
    >
        IMPORT CANVAS
    </button>
</div>

{#if isStickyNoteModalOpen}
    <div
        class="fixed inset-0 bg-black z-20 bg-opacity-70 flex items-center justify-center"
    >
        <div
            class="{isDarkMode
                ? 'bg-gray-800 text-gray-100'
                : 'bg-white text-gray-900'} p-6 max-w-xs w-full rounded-lg shadow-2xl"
            transition:fly={{ y: 20, duration: 300 }}
        >
            <div class="flex justify-between items-center mb-4">
                <h2 class="text-xl font-semibold">Add New Sticky Note</h2>
                <button
                    on:click={closeStickyNoteModal}
                    class="text-xl text-cs-text-light dark:text-gray-400 hover:scale-[120%] cursor-pointer"
                    >&times;</button
                >
            </div>
            {#if isStickyNoteTitleEmpty}
            <p class="text-red-500">Please enter a title</p>
            {/if}
            <input
                id="note-title"
                type="text"
                bind:value={newStickyNoteTitle}
                placeholder="Enter Note Title..."
                class="w-full p-2 mb-4 rounded-md border
                       {isDarkMode
                    ? 'bg-gray-700 border-gray-600 text-gray-100'
                    : 'bg-gray-100 border-gray-300 text-gray-900'}"
                maxlength="50"
            />

            <p class="mb-3">Choose Color:</p>
            <!-- svelte-ignore element_invalid_self_closing_tag -->
            <div class="flex justify-center space-x-4">
                <!-- svelte-ignore a11y_consider_explicit_label -->
                <!-- svelte-ignore element_invalid_self_closing_tag -->
                <button
                    class="w-10 h-10 rounded-md transition duration-150 hover:ring-2 hover:ring-offset-2 hover:ring-note-yellow hover:ring-offset-white dark:hover:ring-offset-gray-800"
                    style="background-color: #fffd85"
                    on:click={() => handleCreateStickyNote("#fffd85")}
                />
                <!-- svelte-ignore element_invalid_self_closing_tag -->
                <!-- svelte-ignore a11y_consider_explicit_label -->
                <button
                    class="w-10 h-10 rounded-md transition duration-150 hover:ring-2 hover:ring-offset-2 hover:ring-note-cyan hover:ring-offset-white dark:hover:ring-offset-gray-800"
                    style="background-color: #93ffef"
                    on:click={() => handleCreateStickyNote("#93ffef")}
                />
                <!-- svelte-ignore a11y_consider_explicit_label -->
                <button
                    class="w-10 h-10 rounded-md transition duration-150 hover:ring-2 hover:ring-offset-2 hover:ring-note-purple hover:ring-offset-white dark:hover:ring-offset-gray-800"
                    style="background-color: #e9aeff"
                    on:click={() => handleCreateStickyNote("#e9aeff")}
                />
            </div>
        </div>
    </div>
{/if}
