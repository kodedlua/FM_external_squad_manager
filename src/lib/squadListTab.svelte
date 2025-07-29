<script lang="ts">
    import { page } from '$app/stores';
    import { supabase } from './supabaseClient';
    import { createEventDispatcher } from 'svelte';
    
    const dispatch = createEventDispatcher();

    let players: string | any[] = [];
    let loading = true;
    let error: string | null = null;

    $: SelectedSquadID = $page.url.searchParams.get('squad');

    $: if(SelectedSquadID) {
        loadPlayers(SelectedSquadID);
    }

    async function loadPlayers(squadId: string) {
        loading = true
        const { data, error: fetchError} = await supabase
            .from('players')
            .select('*')
            .eq('squad_id', squadId)
            .order('created_at', { ascending: false});

        if (fetchError) {
            error = fetchError.message;
            players = []
        } else {
            error = null;
            players = data;
        }
        loading = false;
    }

    function handleImport() {
        dispatch('open');
    }
</script>
{#if loading}
    <div class="overflow-x-auto p-1 shadow-lg rounded-md bg-gray-900">
  <table class="min-w-full table-auto border-collapse">
    <thead>
      <tr class="bg-gray-800 text-white text-left text-sm uppercase">
        <th class="px-2 py-3">ID</th>
        <th class="px-2 py-3">Name</th>
        <th class="px-2 py-3">Position</th>
        <th class="px-2 py-3">Age</th>
        <th class="px-2 py-3">Nationality</th>
        <th class="px-2 py-3">DOB</th>
        <th class="px-2 py-3">CA</th>
        <th class="px-2 py-3">PA</th>
        <th class="px-2 py-3">Better Foot</th>
        <th class="px-2 py-3">Height</th>
        <th class="px-2 py-3">Weight</th>
        <th class="px-2 py-3">JSON Data</th>
        <th class="px-2 py-3">Created At</th>
      </tr>
    </thead>
    <tbody class="text-sm text-white">
      <tr class="bg-gray-900">
        <td class="px-2 py-3"><div class="h-4 bg-gray-700 rounded animate-pulse w-8"></div></td>
        <td class="px-2 py-3"><div class="h-4 bg-gray-700 rounded animate-pulse w-24"></div></td>
        <td class="px-2 py-3"><div class="h-4 bg-gray-700 rounded animate-pulse w-16"></div></td>
        <td class="px-2 py-3"><div class="h-4 bg-gray-700 rounded animate-pulse w-6"></div></td>
        <td class="px-2 py-3"><div class="h-4 bg-gray-700 rounded animate-pulse w-20"></div></td>
        <td class="px-2 py-3"><div class="h-4 bg-gray-700 rounded animate-pulse w-20"></div></td>
        <td class="px-2 py-3"><div class="h-4 bg-gray-700 rounded animate-pulse w-6"></div></td>
        <td class="px-2 py-3"><div class="h-4 bg-gray-700 rounded animate-pulse w-6"></div></td>
        <td class="px-2 py-3"><div class="h-4 bg-gray-700 rounded animate-pulse w-12"></div></td>
        <td class="px-2 py-3"><div class="h-4 bg-gray-700 rounded animate-pulse w-12"></div></td>
        <td class="px-2 py-3"><div class="h-4 bg-gray-700 rounded animate-pulse w-12"></div></td>
        <td class="px-2 py-3"><div class="h-4 bg-gray-700 rounded animate-pulse w-32"></div></td>
        <td class="px-2 py-3"><div class="h-4 bg-gray-700 rounded animate-pulse w-24"></div></td>
      </tr>
      <tr class="bg-gray-900">
        <td class="px-2 py-3"><div class="h-4 bg-gray-700 rounded animate-pulse w-8"></div></td>
        <td class="px-2 py-3"><div class="h-4 bg-gray-700 rounded animate-pulse w-24"></div></td>
        <td class="px-2 py-3"><div class="h-4 bg-gray-700 rounded animate-pulse w-16"></div></td>
        <td class="px-2 py-3"><div class="h-4 bg-gray-700 rounded animate-pulse w-6"></div></td>
        <td class="px-2 py-3"><div class="h-4 bg-gray-700 rounded animate-pulse w-20"></div></td>
        <td class="px-2 py-3"><div class="h-4 bg-gray-700 rounded animate-pulse w-20"></div></td>
        <td class="px-2 py-3"><div class="h-4 bg-gray-700 rounded animate-pulse w-6"></div></td>
        <td class="px-2 py-3"><div class="h-4 bg-gray-700 rounded animate-pulse w-6"></div></td>
        <td class="px-2 py-3"><div class="h-4 bg-gray-700 rounded animate-pulse w-12"></div></td>
        <td class="px-2 py-3"><div class="h-4 bg-gray-700 rounded animate-pulse w-12"></div></td>
        <td class="px-2 py-3"><div class="h-4 bg-gray-700 rounded animate-pulse w-12"></div></td>
        <td class="px-2 py-3"><div class="h-4 bg-gray-700 rounded animate-pulse w-32"></div></td>
        <td class="px-2 py-3"><div class="h-4 bg-gray-700 rounded animate-pulse w-24"></div></td>
      </tr>
    </tbody>
  </table>
</div>
{:else if error}
    <p class="text-red-500">Error: {error}</p>
{:else if players.length === 0}
    <p class="text-gray-500">No players in this squad. Click import below!</p>
{:else}
<div class="overflow-x-auto p-1">
  <table class="min-w-full table-auto border-collapse">
    <thead>
      <tr class="bg-gray-800 text-white text-left text-sm uppercase">
        <th class="px-2 py-3">ID</th>
        <th class="px-2 py-3">Name</th>
        <th class="px-2 py-3">Position</th>
        <th class="px-2 py-3">Age</th>
        <th class="px-2 py-3">Nationality</th>
        <th class="px-2 py-3">DOB</th>
        <th class="px-2 py-3">CA</th>
        <th class="px-2 py-3">PA</th>
        <th class="px-2 py-3">Better Foot</th>
        <th class="px-2 py-3">Height</th>
        <th class="px-2 py-3">Weight</th>
        <th class="px-2 py-3">JSON Data</th>
        <th class="px-2 py-3">Created At</th>
      </tr>
    </thead>
    <tbody class="text-sm text-white">
        {#each players as player}
            <tr class="bg-gray-900 hover:bg-gray-800 transition">
                <td class="px-2 py-3">{player.id}</td>
                <td class="px-2 py-3">{player.full_name}</td>
                <td class="px-2 py-3">{player.position}</td>
                <td class="px-2 py-3">{player.age}</td>
                <td class="px-2 py-3">{player.nationality}</td>
                <td class="px-2 py-3">{player.date_of_birth}</td>
                <td class="px-2 py-3">{player.current_ability}</td>
                <td class="px-2 py-3">{player.potential_ability}</td>
                <td class="px-2 py-3">{player.preferred_foot}</td>
                <td class="px-2 py-3">{player.height_cm}</td>
                <td class="px-2 py-3">{player.weight_kg}</td>
                <td class="px-2 py-3">{JSON.stringify(player.attributes)}</td>
                <td class="px-2 py-3">{player.created_at}</td>
            </tr>
        {/each}
    </tbody>
  </table>
</div>
{/if}
<div class="items-center text-center">
    <button class="rounded-sm bg-blue-500 px-5 py-2 w-auto m-5 mr-0 cursor-pointer hover:inset-shadow-sm hover:text-white text-black" on:click={handleImport}>Import</button>
</div>