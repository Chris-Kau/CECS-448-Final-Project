<script>
    import { onMount } from "svelte";
    import SideNav from "./SideNav.svelte";
    import StickyNote from "./stickynote.svelte";
    import Header from "./Header.svelte";
    import "../app.css";

    let isDarkMode = false;
    let tasks = [];
    let stickyNotes = [];
    let upcomingEvents = [];
    let emptyNoteTitle = false;

    let isTaskModalOpen = false;

    let newTaskTitle = "";
    let newTaskDescription = "";
    let newTaskDate = "";
    let newTaskTag = "Short task";

    function toggleTheme() {
        isDarkMode = !isDarkMode;
    }
    

    // Modal Functions
    function openTaskModal() {
        isTaskModalOpen = true;
    }
    function closeTaskModal() {
        isTaskModalOpen = false;
        emptyNoteTitle = false;
        clearTaskModalFields();
    }

    function clearTaskModalFields() {
        newTaskTitle = "";
        newTaskDescription = "";
        newTaskDate = "";
        newTaskTag = "Short task";
    }

    function addTaskWithDetails() {
        if (newTaskTitle.trim() === "") {
            emptyNoteTitle = true
            return
        };
        emptyNoteTitle = false
        const newTask = {
            id: `task-${Date.now()}`,
            content: newTaskTitle.trim(),
            description: newTaskDescription.trim(),
            date: newTaskDate,
            tag: newTaskTag,
            status: "Todo",
        };
        tasks = [...tasks, newTask];
        closeTaskModal();
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
    
    // --- Canvas Import Logic (Mock Data) ---
    function importedCanvasAssignments() {
        const mockData = [
            [
                "CECS 448 Assignment",
                "do this, do that blah blah blah",
                "2025-12-15T23:59:00",
            ],
            [
                "CECS 323 Final Exam Prep",
                "Study all modules for the final exam.",
                "2025-12-20T10:00:00",
            ],
            [
                "CECS 491A Project Presentation",
                "Create a presentation for your project",
                "2025-12-10T23:59:59",
            ],
        ];

        const newTasks = mockData.map((data, i) => ({
            id: `canvas-task-${Date.now()}-${i}`,
            content: data[0],
            description: data[1],
            date: data[2].substring(0, 16),
            tag: "Short task",
            status: "Todo",
        }));

        tasks = [...tasks, ...newTasks];
        upcomingEvents = newTasks;
    }

    // --- Helper function for task rendering ---
    function formatTaskDate(dateString) {
        if (!dateString) return "";
        return new Date(dateString).toLocaleString("en-US");
    }
</script>

<div 
    class="min-h-screen transition-colors duration-300 {isDarkMode
        ? 'bg-gray-900 text-gray-100'
        : 'bg-gray-100 text-gray-900'}"
>
    <Header {isDarkMode} on:toggleTheme={toggleTheme} />
    <SideNav
        {isDarkMode}
        on:createStickyNote={handleCreateStickyNote}
        on:importCanvas={importedCanvasAssignments}
        on:openTask={openTaskModal}
    />
    

    {#if isTaskModalOpen}
        <div
            class="fixed inset-0 z-50 bg-black bg-opacity-70 flex items-center justify-center"
        >
            <div
                class="{isDarkMode
                    ? 'bg-gray-800 text-gray-100'
                    : 'bg-white text-gray-900'}   p-6 w-full max-w-lg rounded-lg shadow-2xl"
            >
                <div class="flex justify-between items-center mb-4">
                    <h2 class="text-xl font-semibold">Add New Task</h2>
                    <button
                        on:click={closeTaskModal}
                        class="text-xl text-cs-text-light dark:text-gray-400 hover:scale-[120%] cursor-pointer"
                        >&times;</button
                    >
                </div>
                {#if emptyNoteTitle}
                <p class="text-red-500">Please enter a title</p>
                {/if}

                <input
                    type="text"
                    bind:value={newTaskTitle}
                    placeholder="Enter task title... *"
                    class="w-full p-2 mb-3 border rounded-md focus:ring-cs-primary focus:border-cs-primary {isDarkMode
                        ? 'bg-[#00598A]'
                        : 'bg-[#66CCFF]'} border-gray-300 dark:border-gray-600"
                />
                <textarea
                    bind:value={newTaskDescription}
                    placeholder="Enter task description..."
                    class="w-full p-2 mb-3 border rounded-md focus:ring-cs-primary focus:border-cs-primary {isDarkMode
                        ? 'bg-[#00598A]'
                        : 'bg-[#66CCFF]'} border-gray-300 dark:border-gray-600"
                ></textarea>
                <input
                    type="datetime-local"
                    bind:value={newTaskDate}
                    class="w-full p-2 mb-3 border rounded-md focus:ring-cs-primary focus:border-cs-primary {isDarkMode
                        ? 'bg-[#00598A]'
                        : 'bg-[#66CCFF]'} border-gray-300 dark:border-gray-600"
                />

                <label for="taskTag" class="block mb-1 text-sm text-cs-text-light">Duration:</label>
                <select bind:value={newTaskTag} class="w-full p-2 mb-5 border rounded-md focus:ring-cs-primary focus:border-cs-primary 
                        {isDarkMode
                        ? 'bg-[#00598A]'
                        : 'bg-[#66CCFF]'}">
                    <option value="Short task">Short task</option>
                    <option value="Medium task">Medium task</option>
                    <option value="Long task">Long task</option>
                </select>

                <button
                    on:click={addTaskWithDetails}
                    class="w-full {isDarkMode
                        ? 'bg-[#00598A] text-white'
                        : 'bg-[#66CCFF] text-black'} py-2 rounded-md font-semibold transition duration-200 hover:bg-blue-700"
                >
                    Add Task
                </button>
            </div>
        </div>
    {/if}

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
