<script>
    import { onMount } from "svelte";
    import SideNav from "./SideNav.svelte";
    import StickyNote from "./StickyNote.svelte";
    import Header from "./Header.svelte";
    import "../app.css";

    let isDarkMode = false;
    let tasks = [];
    let stickyNotes = [];
    let upcomingEvents = [];
    let emptyNoteTitle = false;
    let noTitle = false;
    let noDesc = false;
    let noDate = false;
    let isTaskModalOpen = false;

    let newTaskTitle = "";
    let newTaskDescription = "";
    let newTaskDate = "";
    let newTaskTag = "Short task";

    function toggleTheme() {
        isDarkMode = !isDarkMode;
    }

    const columns = ["Todo", "In-progress", "Completed"];

    // Modal Functions
    function openTaskModal() {
        noTitle = false;
        noDesc = false;
        noDate = false;
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
        noTitle = false;
        noDesc = false;
        noDate = false;
        if (newTaskTitle.trim() === "") {
            noTitle = true;
        };
        
        if(newTaskDate === ""){
            noDate = true;
        }

        if(newTaskDescription.trim() === ""){
            noDesc = true;
        }
        if(noTitle || noDate || noDesc){
            return
        }
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

    function deleteTask(id) {
        tasks = tasks.filter((t) => t.id !== id);
        upcomingEvents = upcomingEvents.filter((uE) => uE.id !== id);
    }

    // Drag-and-Drop Handlers
    function drag(event, id) {
        event.dataTransfer.setData("text/plain", id);
    }
    function allowDrop(event) {
        event.preventDefault();
    }
    function drop(event, columnId) {
        event.preventDefault();
        const taskId = event.dataTransfer.getData("text/plain");

        const taskIndex = tasks.findIndex((t) => t.id === taskId);
        if (taskIndex !== -1) {
            tasks[taskIndex].status = columnId;
            tasks = tasks;
        }
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

    <main class="ml-[15%] pt-20 p-4">
        <div class="flex mt-4 space-x-4">
            {#each columns as columnId}
                <!-- svelte-ignore a11y_no_static_element_interactions -->
                <div
                    class="column flex-1 p-4 rounded-md shadow-md transition-colors duration-300
                        {isDarkMode
                        ? 'bg-gray-700 text-gray-100 shadow-xl'
                        : 'bg-white text-gray-900 shadow-md'}"
                    on:drop|preventDefault={(e) => drop(e, columnId)}
                    on:dragover={allowDrop}
                >
                    <h2
                        class="text-lg font-normal m-0 {isDarkMode
                            ? 'text-gray-300'
                            : 'text-black'}"
                    >
                        {columnId}
                    </h2>
                    <hr
                        class="border-t {isDarkMode
                            ? 'border-white'
                            : 'border-black'} my-2"
                    />

                    <div class="task-container min-h-[50px]">
                        {#each tasks.filter((t) => t.status === columnId) as task (task.id)}
                            <div
                                draggable="true"
                                on:dragstart={(e) => drag(e, task.id)}
                                class={`p-3 rounded-sm mb-3 border-2 cursor-move relative
                                        ${isDarkMode ? "bg-gray-800" : "bg-gray-50"}`}
                            >
                                <div class="mr-10">
                                    <h3 class="text-base font-normal m-0">
                                        {task.content}
                                    </h3>
                                    <p
                                        class="text-sm text-cs-text-light dark:text-gray-400 my-1"
                                    >
                                        {task.description}
                                    </p>

                                    <div class="flex items-center text-sm my-1">
                                        {#if task.date}
                                            <img
                                                class="w-[18px] h-auto mr-1 {isDarkMode
                                                    ? 'filter invert'
                                                    : ''}"
                                                src="./asset/Calendar.webp"
                                                alt="Date icon"
                                            />
                                            <p class="mr-3">
                                                {formatTaskDate(task.date)}
                                            </p>
                                        {/if}
                                        <span class={`text-sm py-0.5 px-1.5 rounded-md `}>
                                            {task.tag}
                                        </span>
                                    </div>
                                </div>

                                <div
                                    class="absolute right-2 top-1/2 -translate-y-1/2 flex flex-col space-y-1"
                                >
                                    <button
                                        on:click={() => deleteTask(task.id)}
                                        class="p-1 cursor-pointer"
                                    >
                                        <img
                                            class="w-8 h-auto {isDarkMode
                                                ? 'filter invert'
                                                : ''}"
                                            src="./asset/trash.svg"
                                            alt="Delete Task"
                                        />
                                    </button>
                                </div>
                            </div>
                        {/each}
                    </div>
                </div>
            {/each}
        </div>
    </main>

    {#if isTaskModalOpen}
        <div
            class="fixed inset-0 z-50 bg-black bg-opacity-70 flex items-center justify-center"
        >
            <div
                class="{isDarkMode
                    ? 'bg-gray-800 text-gray-100'
                    : 'bg-white text-gray-900'}   p-6 w-full max-w-lg rounded-lg shadow-2xl"
            >
                <div class="flex justify-between items-center mb-2">
                    <h2 class="text-xl font-semibold">Add New Task</h2>
                    <button
                        on:click={closeTaskModal}
                        class="text-xl text-cs-text-light dark:text-gray-400 hover:scale-[120%] cursor-pointer"
                        >&times;</button
                    >
                </div>
                {#if noTitle}
                    <p class="text-red-500 m-0">Please enter a title</p>
                {/if}
                <p>Title: *</p>
                <input
                    type="text"
                    bind:value={newTaskTitle}
                    placeholder="..."
                    class="w-full p-2 m-0 border rounded-md focus:ring-cs-primary focus:border-cs-primary {isDarkMode
                        ? 'bg-[#00598A]'
                        : 'bg-[#66CCFF]'} border-gray-300 dark:border-gray-600"
                />
                {#if noDesc}
                    <p class="text-red-500 m-0">Please enter a description</p>
                {/if}
                <p>Task Description: *</p>
                <textarea
                    bind:value={newTaskDescription}
                    placeholder="..."
                    class="w-full p-2 m-0 border rounded-md focus:ring-cs-primary focus:border-cs-primary {isDarkMode
                        ? 'bg-[#00598A]'
                        : 'bg-[#66CCFF]'} border-gray-300 dark:border-gray-600"
                ></textarea>
                {#if noDate}
                    <p class="text-red-500 m-0">Please enter a due date</p>
                {/if}
                <p>Due Date:</p>
                <input
                    type="datetime-local"
                    bind:value={newTaskDate}
                    class="w-full p-2 m-0 border rounded-md focus:ring-cs-primary focus:border-cs-primary cursor-pointer {isDarkMode
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
