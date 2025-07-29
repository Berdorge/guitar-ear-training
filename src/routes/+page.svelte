<script lang="ts">
    import { onMount } from "svelte";

    const lowESounds: Record<string, string> = {
        E_2: "/sounds/eadgbe/low_e/E_2.mp3",
        F_2: "/sounds/eadgbe/low_e/F_2.mp3",
        "F#_2": "/sounds/eadgbe/low_e/Fsharp_2.mp3",
        G_2: "/sounds/eadgbe/low_e/G_2.mp3",
        "G#_2": "/sounds/eadgbe/low_e/Gsharp_2.mp3",
        A_3: "/sounds/eadgbe/low_e/A_3.mp3",
        "A#_3": "/sounds/eadgbe/low_e/Asharp_3.mp3",
        B_3: "/sounds/eadgbe/low_e/B_3.mp3",
        C_3: "/sounds/eadgbe/low_e/C_3.mp3",
        "C#_3": "/sounds/eadgbe/low_e/Csharp_3.mp3",
        D_3: "/sounds/eadgbe/low_e/D_3.mp3",
        "D#_3": "/sounds/eadgbe/low_e/Dsharp_3.mp3",
        E_3: "/sounds/eadgbe/low_e/E_3.mp3",
    };

    function getRandomNote(): string {
        return notes[(notes.length * Math.random()) << 0];
    }

    function nextRandomNote(): void {
        let nextRandomNote: string;
        do {
            nextRandomNote = getRandomNote();
        } while (nextRandomNote == randomNote);
        randomNote = nextRandomNote;
        noteStates = {};
        if (successful) {
            successfulAttempts += 1;
        }
        allAttempts += 1;
        successful = true;
        playRandomNote();
    }

    function playRandomNote() {
        playNote(randomNote);
    }

    function playNote(noteName: string) {
        const audioElement = document.getElementById(`audio-${noteName}`);
        if (audioElement) {
            audioElement.currentTime = 0;
            audioElement.play();
        }
    }

    function onNoteChosen(note: string) {
        if (note == randomNote) {
            nextRandomNote();
        } else {
            noteStates[note] = "bg-red-500";
            playNote(note);
            successful = false;
        }
    }

    function removeNote(removedNote: string) {
        notes = notes.filter((note) => note != removedNote);
        successful = true;
        successfulAttempts = 0;
        allAttempts = 0;
        noteStates = {};
        randomNote = getRandomNote();
        playRandomNote();
    }

    let notes = $state(Object.keys(lowESounds));
    let randomNote: string = $state(getRandomNote());
    let noteStates: Record<string, string> = $state({});
    let successful: boolean = $state(true);
    let successfulAttempts: number = $state(0);
    let allAttempts: number = $state(0);

    onMount(() => {
        playRandomNote();
    });
</script>

<h1 class="text-2xl font-bold text-center mb-3">
    Correct attempts: {successfulAttempts}/{allAttempts}
    {#if allAttempts > 0}
        ({(successfulAttempts / allAttempts) * 100}%)
    {/if}
</h1>

<button
    type="button"
    class="m-auto block max-w-max cursor-pointer rounded-lg border-2 p-2"
    onclick={playRandomNote}
>
    Play the random note
</button>

<div
    class="notes-holder px-16 py-8 flex flex-wrap justify-center items-center content-center gap-8"
>
    {#each notes as note}
        <div
            class="note-button flex-auto flex flex-nowrap border-2 rounded-lg {noteStates[
                note
            ]}"
        >
            <button
                type="button"
                class="note-button-play p-2 flex-none flex items-center justify-center cursor-pointer"
                aria-label="play"
                onclick={() => playNote(note)}
            >
                <i class="fa-solid fa-play"></i></button
            >
            <div class="note-button-divider w-0.5 self-stretch bg-black"></div>
            <button
                class="note-button-name flex-auto flex items-center justify-center p-2 cursor-pointer"
                onclick={() => onNoteChosen(note)}
            >
                {note}
            </button>
            <div class="note-button-divider w-0.5 self-stretch bg-black"></div>
            <button
                type="button"
                class="note-button-play p-2 flex-none flex items-center justify-center cursor-pointer"
                aria-label="remove"
                onclick={() => removeNote(note)}
            >
                <i class="fa-solid fa-xmark"></i></button
            >
        </div>
        <audio id="audio-{note}" src={lowESounds[note]}></audio>
    {/each}
</div>
