<script>
    import { createEventDispatcher } from "svelte";
    import "../app.css";
    export let title;
    export let note;
    const dispatch = createEventDispatcher();

    let description = note.description || "";

    let isDragging = false;
    let offsetX = 0;
    let offsetY = 0;
    let noteElement;

    function handleMouseDown(e) {
        isDragging = true;
        offsetX = e.clientX - note.x;
        offsetY = e.clientY - note.y;

        document.addEventListener("mousemove", handleMouseMove);
        document.addEventListener("mouseup", handleMouseUp);
    }

    function handleMouseMove(e) {
        if (!isDragging) return;

        note.x = e.clientX - offsetX;
        note.y = e.clientY - offsetY;
    }

    function handleMouseUp() {
        isDragging = false;
        document.removeEventListener("mousemove", handleMouseMove);
        document.removeEventListener("mouseup", handleMouseUp);

        dispatch("update", { note });
    }

    function handleDelete() {
        dispatch("delete", { id: note.id });
    }

    let timeout;
    function handleKeyup() {
        clearTimeout(timeout);
        timeout = setTimeout(() => {
            note.description = description;
            dispatch("update", { note });
        }, 500);
    }
</script>

<div
    bind:this={noteElement}
    class="absolute z-50 flex flex-col w-[200px] h-[200px] rounded-md shadow-lg border border-gray-300"
    style="background-color: {note.color}; left: {note.x}px; top: {note.y}px;"
    class:cursor-grabbing={isDragging}
>
    <!-- svelte-ignore a11y_no_static_element_interactions -->
    <div
        class="p-1 px-2 h-7 cursor-move z-20 text-sm font-semibold text-gray-700 shadow-sm"
        style="background-color: {note.color}; filter: brightness(0.9);"
        on:mousedown={handleMouseDown}
    >
        <span class="mr-4">= {title}</span>
        <button
            on:click={handleDelete}
            class="float-right text-base text-cs-text-light hover:text-black hover:scale-125 transition cursor-pointer"
            >&times;</button
        >
    </div>

    <div class="grow flex justify-center p-2 pt-3">
        <textarea
            class="w-[90%] h-[90%] p-1 text-sm rounded-md resize-none border border-gray-400 focus:ring-1 focus:ring-cs-primary focus:border-cs-primary text-black"
            bind:value={description}
            on:keyup={handleKeyup}
            style="background-color: {note.color};"
        ></textarea>
    </div>
</div>
