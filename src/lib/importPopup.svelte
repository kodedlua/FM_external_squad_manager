<script lang="ts">
	import { page } from '$app/stores';
	import { json } from '@sveltejs/kit';
    import { createEventDispatcher } from 'svelte';
	import { supabase } from './supabaseClient';
	import { goto } from '$app/navigation';
    const dispatch = createEventDispatcher();

    const statsColumns = [
        'Agg', 'Aer', 'Agi', 'Ant', 'Bal', 'Bra', 'Cmd', 'Com', 'Cmp', 'Cnt', 'Cor', 'Cro', 'Dec', 'Det', 'Dri',
        'Ecc', 'Fin', 'Fir', 'Fla', 'Fre', 'Han', 'Hea', 'Jum', 'Kic', 'Ldr', 'Lon', 'L Th', 'Mar', 'Nat',
        'OtB', '1v1', 'Pac', 'Pas', 'Pen', 'Pos', 'Pun', 'Ref', 'TRO', 'Sta', 'Str', 'Tck', 'Tea', 'Tec',
        'Thr', 'Vis', 'Wor', 'Acc'
    ];

    $: SelectedSquadID = $page.url.searchParams.get('squad');

    let selectedFile = null;
    let fileContent = '';
    let players = [];

    function close() {
        dispatch('close');
    }

    // @ts-ignore
    function onFileChange(event) {
        selectedFile = event?.target.files[0];
    }

    // @ts-ignore
    function handleFile(event) {
        // @ts-ignore
        if (!selectedFile) return;

        const reader = new FileReader();
        reader.onload = async () => {
            // @ts-ignore
            fileContent = reader.result;
            console.log("RTF raw: ", fileContent);
            parseRTF(fileContent);
            await insertIntoDB();
            dispatch('close');
            goto($page.url.href, { replaceState: true });
        }    
        reader.readAsText(selectedFile);
    }

    function parseRTF(fileContent) {
        let lines = fileContent.split('\n').map(l => l.trim());

        const dataLines = lines.filter(line => line.startsWith("|") && !line.startsWith('| ---'));
        for (const line of dataLines) {
            const parts = line.split('|').map(p => p.trim());
            const beforeStats = parts.slice(1, 11);
            if (beforeStats[0] === "Name") continue;
            const statsValues = parts.slice(11,statsColumns.length-1);

            const stats: Record<string, number> = {};
            for (let i = 0; i < statsColumns.length-1; i++) {
                const val = parseInt(statsValues[i], 10);
                // @ts-ignore
                stats[statsColumns[i]] = isNaN(val) ? 0 : val;
            }
            
            const datePart = beforeStats[4].split(' ')[0];
            const [dayStr, monthStr, yearStr] = datePart.split('/');

            const day = parseInt(dayStr, 10);
            const month = parseInt(monthStr, 10);
            const year = parseInt(yearStr, 10);

            const dateObj = new Date(year, month, day);
            const player = {
                full_name: beforeStats[0],
                position: beforeStats[1],
                age: parseInt(beforeStats[2], 10),
                nationality: beforeStats[3],
                day_of_birth: dateObj.toISOString().split('T')[0],
                current_ability: parseInt(beforeStats[5], 10),
                potential_ability: parseInt(beforeStats[6], 10),
                preferred_foot: beforeStats[7],
                height_cm: parseInt(beforeStats[8].split(" ")[0], 10),
                weight_kg: parseInt(beforeStats[9].split(" ")[0], 10),
                attributes: JSON.stringify(stats),
            }
            players.push(player);
        }
    }

    async function insertIntoDB() {

        const { error: deleteError } = await supabase
            .from('players')
            .delete()
            .eq('squad_id', SelectedSquadID);

        const sanitizedPlayers = players.map(player => ({
            full_name: player.full_name,
            position: player.position,
            age: player.age,
            nationality: player.nationality,
            date_of_birth: player.day_of_birth,
            current_ability: player.current_ability,
            potential_ability: player.potential_ability,
            preferred_foot: player.preferred_foot,
            height_cm: player.height_cm,
            weight_kg: player.weight_kg,
            attributes: JSON.parse(player.attributes),
            squad_id: SelectedSquadID
        }));
        
        const { data, error } = await supabase
            .from('players')
            .insert(sanitizedPlayers);
    }


</script>

<div class="bg-gray-900 w-xl absolute top-25 text-center rounded-sm p-4 items-center">
    <h1 class="text-center text-3xl">Import Players</h1>
    <p>from FM24</p>
    <div>
        <label class="rounded-sm bg-gray-800 px-5 py-2 w-auto m-5 mr-0 cursor-pointer hover:inset-shadow-sm inline-block">
            Wybierz plik
            <input type="file" class="hidden" accept=".rtf" on:change={onFileChange} />
        </label>
        <button class="rounded-sm bg-blue-500 px-5 py-2 w-auto m-5 mr-0 cursor-pointer hover:inset-shadow-sm hover:text-white text-black" on:click={handleFile}>OK</button>
    </div>
    <button class="rounded-sm bg-blue-500 px-5 py-2 w-auto m-5 mr-0 cursor-pointer hover:inset-shadow-sm hover:text-white text-black" on:click={close}>Zamknij</button>
</div>