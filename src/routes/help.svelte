<script lang="ts">
    import { onMount } from 'svelte';

    function closeWindow() {
        window.close();
    }

    onMount(() => {
        document.title = "Chordle Help";
    });
</script>

<svelte:head>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css">
</svelte:head>

<style>
    :global(body) {
        background-image: url('$lib/backround.svg');
        background-size: cover;
        background-repeat: no-repeat;
        background-attachment: fixed;
        color: #000000;
        transition: color 0.3s;
        font-size: 1rem;
        min-width: 100vw;
        min-height: 100vh;
        overflow-x: hidden;
    }
    .card {
        background-color: rgba(255, 255, 255, 0.1);
        backdrop-filter: blur(0.625rem);
        border: none;
        border-radius: 0.5rem;
        color: #000000;
        width: 90%;
        max-width: 50rem;
        margin: 1.25rem auto;
        position: relative;
    }
    .card-title {
        font-size: 1.5rem;
        font-weight: bold;
        margin-bottom: 0.9375rem;
        text-align: center;
    }
    .card-body {
        padding: 1.25rem;
    }
    .back-button {
        position: absolute;
        top: 1rem;
        right: 1rem;
        background-color: rgba(255, 255, 255, 0.1);
        border: 0.0625rem solid rgba(255, 255, 255, 0.2);
        border-radius: 0.5rem;
        padding: 0.5rem 1rem;
        color: #000000;
        cursor: pointer;
        transition: all 0.3s ease;
    }
    .back-button:hover {
        background-color: rgba(255, 255, 255, 0.2);
        border-color: rgba(255, 255, 255, 0.5);
    }
</style>
<body>

    <div class="card">
        <div class="card-body">
            <h2 class="card-title">Welcome to Chordle</h2>
            
            <h3 class="card-subtitle mb-3">How to Use?</h3>
            <ol>
                <li>Select a root note from the first dropdown menu. This will be the starting note of your scale.</li>
                <li>Choose a mode from the second dropdown menu. This determines the pattern of intervals in your scale.</li>
                <li>The first card displays the scale notes based on your selections, along with their corresponding Roman numerals.</li>
                <li>The second card shows the chord notes for each degree of the scale.</li>
                <li>Click the toggle button in the second card to switch between viewing chord notes and chord building information.</li>
                <li>Experiment with different root notes and modes to explore various scales and their corresponding chords.</li>
            </ol>
            
            <h3 class="card-subtitle mb-3 mt-4">Why Chordle?</h3>
            <ul>
                <li><strong>Music Theory Learning:</strong> Chordle helps you visualize and understand the relationship between scales, modes, and chords.</li>
                <li><strong>Composition Tool:</strong> Use it to find interesting chord progressions or to understand the harmonic structure of different modes.</li>
                <li><strong>Practice Aid:</strong> For musicians, it's a quick reference to see scale degrees and chord constructions in any key or mode.</li>
                <li><strong>Exploration:</strong> Discover how changing the root note or mode affects the resulting scale and chords.</li>
                <li><strong>Accessibility:</strong> Provides an easy-to-use interface for both beginners and advanced musicians to explore music theory concepts.</li>
            </ul>
            
            <p class="mt-4">Whether you're a beginner learning music theory, a composer looking for inspiration, or an experienced musician wanting a quick reference, Chordle is designed to be your go-to tool for exploring scales and chords across different modes and keys.</p>
            
            
        </div>
    </div>
    <h1 class="card-title font-weight-bold font-size-50">Have Fun!</h1>
</body>s