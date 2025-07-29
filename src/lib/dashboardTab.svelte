<script lang="ts">
    import Formation from '$lib/formations.svelte';

    const positions = {
        GK: { name: "Goalkeeper", x: 50, y: 90 },
        LB: { name: "Left Back", x: 20, y: 70 },
        CB1: { name: "Center Back 1", x: 40, y: 70 },
        CB2: { name: "Center Back 2", x: 60, y: 70 },
        RB: { name: "Right Back", x: 80, y: 70 },
        LM: { name: "Left Midfielder", x: 20, y: 50 },
        CM1: { name: "Center Midfielder 1", x: 40, y: 50 },
        CM2: { name: "Center Midfielder 2", x: 60, y: 50 },
        RM: { name: "Right Midfielder", x: 80, y: 50 },
        LW: { name: "Left Winger", x: 30, y: 30 },
        CF: { name: "Center Forward", x: 50, y: 30 },
        RW: { name: "Right Winger", x: 70, y: 30 },
        CM3: { name: "Center Midfielder 3", x: 50, y: 50 },
        CB3: { name: "Center Back 3", x: 50, y: 70 },
        CF1: { name: "Center Forward 1", x: 45, y: 30 },
        CF2: { name: "Center Forward 2", x: 55, y: 30 }
    };

    const formations = {
        "4-4-2": ["GK", "LB", "CB1", "CB2", "RB", "LM", "CM1", "CM2", "RM", "LW", "RW"],
        "4-3-3": ["GK", "LB", "CB1", "CB2", "RB", "CM1", "CM2", "CM3", "LW", "CF", "RW"],
        "3-5-2": ["GK", "CB1", "CB2", "CB3", "LM", "CM1", "CM2", "CM3", "RM", "CF1", "CF2"]
    };

    let currentFormation = "4-4-2";
</script>




<div class="text-center items-center relative">
    <h1 class="text-3xl p-4">Your squad</h1>
    <div class="relative bg-gray-800 h-255 w-[100%]">  
        <div class="text-left absolute m-5 p-5">
            <select
                id="formations"
                class="w-full p-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-2 focus:ring-green-600"
                bind:value={currentFormation}
            >
                {#each Object.keys(formations) as form}
                <option value={form}>{form}</option>
                {/each}
            </select>

            <Formation formation={formations[currentFormation]} positions={positions} />
        </div>
    </div>
</div>