<script lang="ts">
	import { onMount } from 'svelte';
	import { writable } from 'svelte/store';
	import HelpWindow from './help.svelte';

	let rootnoteSelected = true;
	let modeSelected = true;
	let showChordBuilding = false;
	let showHelpWindow = false;

	const rootnote = writable('C');
	const mode = writable('Ionian');

	const notes = ['C', 'C#', 'D', 'D#', 'E', 'F', 'F#', 'G', 'G#', 'A', 'A#', 'B'];
	const romanNumerals = ['I', 'II', 'III', 'IV', 'V', 'VI', 'VII'];
	const colors = ['#FF5733', '#33FF57', '#3357FF', '#FF33F1', '#33FFF1', '#FFD700', '#8A2BE2'];

	type Mode = 'Ionian' | 'Dorian' | 'Phrygian' | 'Lydian' | 'Mixolydian' | 'Aeolian' | 'Locrian' | 'Blues' | 'Jazz' | 'Harmonic Minor' | 'Melodic Minor' | 'Pentatonic Major' | 'Pentatonic Minor';
    onMount(() => {
        document.title = "Chordle - Interactive Chord Builder and Scale Finder";
    });
  
	function getScaleNotes(root: string, mode: Mode): string[] {
		const allNotes = ['C', 'C#', 'D', 'D#', 'E', 'F', 'F#', 'G', 'G#', 'A', 'A#', 'B'];
		const modeIntervals: Record<Mode, number[]> = {
			'Ionian':           [0, 2, 4, 5, 7, 9, 11],
			'Dorian':           [0, 2, 3, 5, 7, 9, 10],
			'Phrygian':         [0, 1, 3, 5, 7, 8, 10],
			'Lydian':           [0, 2, 4, 6, 7, 9, 11],
			'Mixolydian':       [0, 2, 4, 5, 7, 9, 10],
			'Aeolian':          [0, 2, 3, 5, 7, 8, 10],
			'Locrian':          [0, 1, 3, 5, 6, 8, 10],
			'Blues':            [0, 3, 5, 6, 7, 10],
			'Jazz':             [0, 2, 3, 5, 7, 9, 10],
			'Harmonic Minor':   [0, 2, 3, 5, 7, 8, 11],
			'Melodic Minor':    [0, 2, 3, 5, 7, 9, 11],
			'Pentatonic Major': [0, 2, 4, 7, 9],
			'Pentatonic Minor': [0, 3, 5, 7, 10]
		};
		
		const rootIndex = allNotes.indexOf(root);
		return modeIntervals[mode].map(interval => allNotes[(rootIndex + interval) % 12]);
	}

	$: scaleNotes = getScaleNotes($rootnote, $mode as Mode);

	function getChordNotes(scaleNotes: string[]): string[][] {
		return scaleNotes.map((_, i) => {
			return [
				scaleNotes[i],
				scaleNotes[(i + 2) % 7],
				scaleNotes[(i + 4) % 7]
			];
		});
	}

	$: chordNotes = getChordNotes(scaleNotes);

	function getChordBuilding(scaleNotes: string[]): string[] {
		const chordTypes = ['Major', 'Minor', 'Minor', 'Major', 'Major', 'Minor', 'Diminished'];
		return scaleNotes.map((note, i) => `${note} ${chordTypes[i]}: ${romanNumerals[i]} - ${romanNumerals[(i + 2) % 7]} - ${romanNumerals[(i + 4) % 7]}`);
	}

	$: chordBuilding = getChordBuilding(scaleNotes);

	onMount(() => {
		const rootnoteSelect = document.getElementById('rootnote') as HTMLSelectElement;
		const modeSelect = document.getElementById('mode') as HTMLSelectElement;
	
		rootnoteSelect.value = $rootnote;
		modeSelect.value = $mode;

		rootnoteSelect.addEventListener('change', () => {
			rootnoteSelected = true;
			rootnote.set(rootnoteSelect.value);
		});

		modeSelect.addEventListener('change', () => {
			modeSelected = true;
			mode.set(modeSelect.value);
		});
	});

	function toggleChordBuilding() {
		showChordBuilding = !showChordBuilding;
	}

	function toggleHelpWindow() {
		showHelpWindow = !showHelpWindow;
	}

	function getRandomElement<T>(arr: T[]): T {
		return arr[Math.floor(Math.random() * arr.length)];
	}

	function selectRandomRootnoteAndMode() {
		rootnote.set(getRandomElement(notes));
		mode.set(getRandomElement(['Ionian', 'Dorian', 'Phrygian', 'Lydian', 'Mixolydian', 'Aeolian', 'Locrian', 'Blues', 'Jazz', 'Harmonic Minor', 'Melodic Minor', 'Pentatonic Major', 'Pentatonic Minor']));
	}
</script>

<svelte:head>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <meta name="description" content="Chordle is an interactive chord builder and scale finder. Explore musical scales, modes, and chords with this easy-to-use tool.">
    <meta name="keywords" content="chord builder, scale finder, music theory, musical modes, chord progression">
    <link rel="canonical" href="https://chordle.com">
    <meta property="og:title" content="Chordle - Interactive Chord Builder and Scale Finder">
    <meta property="og:description" content="Explore musical scales, modes, and chords with this easy-to-use interactive tool.">
    <meta property="og:url" content="https://chordle.com">
    <meta property="og:type" content="website">
    <meta name="twitter:card" content="summary_large_image">
    <meta name="twitter:title" content="Chordle - Interactive Chord Builder and Scale Finder">
    <meta name="twitter:description" content="Explore musical scales, modes, and chords with this easy-to-use interactive tool.">
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

	:global(.form-select), :global(.form-control) {
		background-color: rgba(255, 255, 255, 0.1);
		color: #000000;
		border: 0.0625rem solid rgba(255, 255, 255, 0.2);
		transition: all 0.3s ease;
		backdrop-filter: blur(0.625rem);
		border-radius: 0.5rem;
	}

	:global(.form-select:focus), :global(.form-control:focus) {
		background-color: rgba(255, 255, 255, 0.2);
		color: #000000;
		border-color: rgba(255, 255, 255, 0.5);
		box-shadow: 0 0 0 0.125rem rgba(255, 255, 255, 0.25);
	}

	:global(.form-select option) {
		background-color: rgba(255, 255, 255, 0.9);
		color: #000000;
	}

	:global(h1), :global(h2), :global(h3), :global(h5) {
		color: #000000;
	}

	:global(label) {
		color: #000000;
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

	.note-container {
		display: flex;
		flex-wrap: wrap;
		justify-content: center;
		width: 100%;
	}

	.note-item {
		flex: 0 0 calc(14.28% - 0.625rem);
		text-align: center;
		padding: 0.625rem 0.3125rem;
		background-color: rgba(0, 0, 0, 0.6);
		border-radius: 0.5rem;
		margin: 0.3125rem;
		box-shadow: 0 0 0.25rem rgba(0, 0, 0, 0.1);
		display: flex;
		flex-direction: column;
		justify-content: center;
		min-width: 5rem;
	}

	.toggle-button {
		position: fixed;
		top: 0.625rem;
		right: 0.625rem;
		z-index: 1;
		padding: 0.5rem 1rem;
		font-size: 0.875rem;
		font-weight: bold;
		border-radius: 1.25rem;
		background-color: rgba(255, 255, 255, 0.2);
		color: #000000;
		border: none;
		transition: all 0.3s ease;
	}

	.toggle-button:hover {
		background-color: rgba(255, 255, 255, 0.3);
	}

	.toggle-button:active {
		background-color: rgba(255, 255, 255, 0.4);
	}

	.headline {
		font-size: 3rem;
		font-weight: bold;
		text-align: center;
		margin-bottom: 1.25rem;
	}

	.select-container {
		display: flex;
		justify-content: center;
		gap: 0.625rem;
		margin: 0 auto 1.25rem;
		width: 90%;
		max-width: 25rem;
		align-items: center;
	}

	.select-container select {
		font-size: 1rem;
		padding: 0.5rem;
	}

	@media (max-width: 48rem) {
		.select-container {
			width: 90%;
		}

		.select-container select {
			font-size: 0.875rem;
			padding: 0.375rem;
		}
	}

	.select-container .form-select {
		width: 100%;
		max-width: 12.5rem;
		height: 2.5rem;
		font-size: 1rem;
		background-color: rgba(255, 255, 255, 0.1);
		backdrop-filter: blur(0.625rem);
		border: none;
		border-radius: 0.5rem;
		color: #000000;
		appearance: none;
		background-image: url("data:image/svg+xml;charset=US-ASCII,%3Csvg%20xmlns%3D%22http%3A%2F%2Fwww.w3.org%2F2000%2Fsvg%22%20width%3D%22292.4%22%20height%3D%22292.4%22%3E%3Cpath%20fill%3D%22%23000000%22%20d%3D%22M287%2069.4a17.6%2017.6%200%200%200-13-5.4H18.4c-5%200-9.3%201.8-12.9%205.4A17.6%2017.6%200%200%200%200%2082.2c0%205%201.8%209.3%205.4%2012.9l128%20127.9c3.6%203.6%207.8%205.4%2012.8%205.4s9.2-1.8%2012.8-5.4L287%2095c3.5-3.5%205.4-7.8%205.4-12.8%200-5-1.9-9.2-5.5-12.8z%22%2F%3E%3C%2Fsvg%3E");
		background-repeat: no-repeat;
		background-position: right 0.625rem top 50%;
		background-size: 0.75rem auto;
		padding-right: 1vw;
	}

	@media (max-width: 48rem) {
		.select-container .form-select {
			background-size: 0.625rem auto;
			padding-right: 1.5625rem;
		}
	}

	.note-item p {
		color: var(--note-color);
		text-shadow: 0.0625rem 0.0625rem 0.125rem rgba(0, 0, 0, 0.5);
	}

	.note {
		font-size: 1.125rem;
		font-weight: bold;
		margin: 0;
	}

	.roman-numeral {
		font-size: 0.875rem;
		margin: 0;
	}

	.chord-info {
		font-size: 0.875rem;
		margin: 0;
		display: flex;
		flex-direction: column;
		align-items: center;
	}

	.card-title {
		font-size: 1.5rem;
		font-weight: bold;
		margin-bottom: 0.9375rem;
		text-align: center;
	}

	@media (max-width: 48rem) {
		.headline {
			font-size: 2.25rem;
		}
		.select-container {
			flex-direction: row;
			flex-wrap: wrap;
			justify-content: center;
		}
		.select-container .form-select {
			width: calc(50% - 0.3125rem);
			max-width: none;
		}
		.note-item {
			flex: 0 0 calc(25% - 0.625rem);
		}
	}

	@media (max-width: 30rem) {
		.headline {
			font-size: 1.75rem;
		}
		.note-item {
			flex: 0 0 calc(33.33% - 0.625rem);
		}
		.card-title {
			font-size: 1.25rem;
		}
		.select-container .form-select {
			width: 100%;
			margin-bottom: 0.625rem;
		}
	}

	@media screen and (min-width: 75rem) {
		.card {
			max-width: 62.5rem;
		}
		.note-item {
			flex: 0 0 calc(14.28% - 0.625rem);
			min-width: 6.25rem;
		}
		.note {
			font-size: 1.25rem;
		}
		.roman-numeral {
			font-size: 1rem;
		}
	}

	@media screen and (min-width: 100rem) {
		.card {
			max-width: 75rem;
		}
		.note-item {
			flex: 0 0 calc(14.28% - 0.9375rem);
			min-width: 7.5rem;
		}
		.note {
			font-size: 1.375rem;
		}
		.roman-numeral {
			font-size: 1.125rem;
		}
	}

	.footer {
		display: flex;
		justify-content: center;
		align-items: center;
		margin-top: 2vh;
	}

	.footer a {
		margin: 0
    }

.footer a {
    margin: 0 1rem;
    color: #000000;
    text-decoration: none;
    display: flex;
    align-items: center;
}

.footer a img {
    margin-right: 0.5rem;
    width: 1.5rem;
    height: 1.5rem;
}
</style>

{#if showHelpWindow}
<HelpWindow />
<button class="toggle-button" style="position: fixed; top: 1rem; right: 1rem;" on:click={toggleHelpWindow}>
    Back
</button>
{:else}
<div class="d-flex flex-column align-items-center" style="margin-top: 3vh;">
    <h1 class="headline">Chordle</h1>
    <p class="lead text-center mb-4">Explore musical scales, modes, and build chords with this interactive tool.</p>
    <div class="select-container">
        <select class="form-select" id="rootnote">
            <option>C</option>
            <option>C#</option>
            <option>D</option>
            <option>D#</option>
            <option>E</option>
            <option>F</option>
            <option>F#</option>
            <option>G</option>
            <option>G#</option>
            <option>A</option>
            <option>A#</option>
            <option>B</option>
        </select>
        <select class="form-select" id="mode">
            <option>Ionian</option>
            <option>Dorian</option>
            <option>Phrygian</option>
            <option>Lydian</option>
            <option>Mixolydian</option>
            <option>Aeolian</option>
            <option>Locrian</option>
            <option>Blues</option>
            <option>Jazz</option>
            <option>Harmonic Minor</option>
            <option>Melodic Minor</option>
            <option>Pentatonic Major</option>
            <option>Pentatonic Minor</option>
        </select>
        <span style="align-self: center;">or</span>
        <button class="btn btn-primary" on:click={selectRandomRootnoteAndMode} style="display: flex; flex-direction: column;">
       Random Scale
        </button>
    </div>
</div>
<div class="d-flex justify-content-center" style="margin-top: 2vh;">
    <div class="card">
        <div class="card-body d-flex flex-column justify-content-center align-items-center" style="min-height: 20vh;">
            <h2 class="card-title">
                {$rootnote} {$mode}
            </h2>
            <div class="note-container">
                {#each scaleNotes as note, i}
                    <div class="note-item" style="--note-color: {colors[i]};">
                        <p class="note">{note}</p>
                        <p class="roman-numeral">{romanNumerals[i]}</p>
                    </div>
                {/each}
            </div>
        </div>
    </div>
</div>
<div class="d-flex justify-content-center" style="margin-top: 2vh;">
    <div class="card">
        <div class="card-body d-flex flex-column justify-content-center align-items-center" style="min-height: 20vh;">
            <h2 class="card-title">Chord Notes</h2>

            <div class="note-container">
                {#each scaleNotes as note, i}
                    <div class="note-item" style="--note-color: {colors[i]};">
                        {#if showChordBuilding}
                            <div class="chord-info">
                                <p>{chordBuilding[i].split(':')[0]}</p>
                                <p>{chordBuilding[i].split(':')[1]}</p>
                            </div>
                        {:else}
                            {#each chordNotes[i].slice().reverse() as chordNote}
                                <p class="note">{chordNote}</p>
                            {/each}
                            <p class="roman-numeral">{romanNumerals[i]}</p>
                        {/if}
                    </div>
                {/each}
            </div>
        </div>
    </div>
</div>
<div class="footer" style="display: flex; flex-wrap: wrap; justify-content: center; gap: 1rem;">
    <a href="https://www.instagram.com/HappyWachtelJuan" target="_blank" style="display: flex; align-items: center;">
        <img src="https://upload.wikimedia.org/wikipedia/commons/a/a5/Instagram_icon.png" alt="Instagram" style="width: 24px; height: 24px;">
        <span style="margin-left: 0.5rem;">HappyWachtelJuan</span>
    </a>
    <a>This is an Open Source Project :D</a>
    <a href="https://github.com/Zorroinvader" target="_blank" style="display: flex; align-items: center;">
        <img src="https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png" alt="Github" style="width: 24px; height: 24px;">
        <span style="margin-left: 0.5rem;">Zorroinvader</span>
    </a>
</div>
<button class="toggle-button" style="position: fixed; top: 1rem; right: 1rem;" on:click={toggleHelpWindow}>
    ?
</button>
{/if}

<footer class="mt-5 pb-4">
<div class="container">

</div>
</footer>