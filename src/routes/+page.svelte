<script>
    import Header from "./Header.svelte";
    import SideNav from "./SideNav.svelte";
    import "../app.css";

    let isDarkMode = false;

    function toggleTheme() {
        isDarkMode = !isDarkMode;
    }


</script>

<div class="min-h-screen transition-colors duration-300 {isDarkMode
        ? 'bg-gray-900 text-gray-100'
        : 'bg-gray-100 text-gray-900'}">
    <Header {isDarkMode} on:toggleTheme={toggleTheme} />
    <SideNav
        {isDarkMode}
    />


</div>
