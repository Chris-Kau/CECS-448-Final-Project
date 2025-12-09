<script>
    import Header from "./Header.svelte";
    import SideNav from "./SideNav.svelte";
    import StickyNote from "./stickynote.svelte";
    import "../app.css";

    let isDarkMode = false;
    let stickyNotes = [];

    function toggleTheme() {
        isDarkMode = !isDarkMode;
    }

    // --- Sticky Note Logic ---
    function handleCreateStickyNote(event) {
        const { title, color } = event.detail;
        const newNote = {
            id: "stickyNote" + Date.now(),
            color: color,
            x: 200,
            y: 200,
            title: title,
            description: "",
        };
        console.log(newNote);
        stickyNotes = [...stickyNotes, newNote];
    }

    function handleStickyNoteUpdate(event) {
        const updatedNote = event.detail.note;
        stickyNotes = stickyNotes.map((n) =>
            n.id === updatedNote.id ? updatedNote : n,
        );
    }

    function handleStickyNoteDelete(event) {
        const { id } = event.detail;
        stickyNotes = stickyNotes.filter((n) => n.id !== id);
    }
</script>

<div class="min-h-screen transition-colors duration-300 {isDarkMode
        ? 'bg-gray-900 text-gray-100'
        : 'bg-gray-100 text-gray-900'}">
    <Header {isDarkMode} on:toggleTheme={toggleTheme} />
    <SideNav
        {isDarkMode}
        on:createStickyNote={handleCreateStickyNote}

    />


    <div id="stickyNotesContainer" class="z-50">
        {#each stickyNotes as note (note.id)}
            <StickyNote
                {note}
                title={note.title}
                on:update={handleStickyNoteUpdate}
                on:delete={handleStickyNoteDelete}
            />
        {/each}
    </div>
</div>
