<script>
    import SquadListTab from "$lib/squadListTab.svelte";
    import DashboardTab from "$lib/dashboardTab.svelte";
    import ImportPopup from "$lib/importPopup.svelte";
	import { supabase } from "$lib/supabaseClient";
	import { page } from '$app/stores';
	import { goto } from "$app/navigation";

    const tabs = [
        { id: 1, label: 'Builder', component: DashboardTab},
        { id: 2, label: 'Squad List', component: SquadListTab}
    ]

    let activeTab = tabs[0].id;
    let showPopupLayer = false;
    /**
	 * @type {any[]}
	 */
    let squads = [];
    /**
	 * @type {string | null}
	 */
    let SelectedSquadID = null;


    function handlePopupOpen() {
        showPopupLayer = true;
    }

    function handlePopupClose() {
        showPopupLayer = false
    }

    async function loadSquads() {
        const { data, error } = await supabase
            .from('squads')
            // @ts-ignore
            .select('*')
            .order('created_at', { ascending: true});

        if (error) {
            console.log(error)
            return;
        }
        console.log(data);
        squads = data;
    }

    loadSquads();

    $: ActiveComponent = tabs.find(tab => tab.id === activeTab)?.component;
    $: SelectedSquadID = $page.url.searchParams.get('squad');

    // @ts-ignore
    function handleChange(event) {
        SelectedSquadID = event.target.value;
        goto(`?squad=${SelectedSquadID}`, {replaceState: true});
    }

</script>
<header class="flex items-center justify-between h-15">
    <div class="relative pl-4 pt-2 h-10 w-63">
        <h1><span class="text-purple-700">FM24</span> External Squad Manager</h1>
        <p class="text-gray-500 text-center">by koded070</p>
    </div>
    <div class="relative h-10 pt-2 w-auto">
        <select bind:value={SelectedSquadID} on:change={handleChange} class="absolute right-0 rounded-sm bg-white border-0 px-5 py-2 w-auto mr-5 cursor-pointer hover:inset-shadow-sm text-black">
                <option value="" disabled selected>Choose squad id</option>
                {#each squads as squad}
                    <option value={squad.id}>{squad.name} ({squad.author})</option>
                {/each}
        </select>
    </div>
</header>
<nav class="items-center flex w-auto bg-gray-800 text-black mt-5 h-15">
    {#each tabs as tab}
        <button on:click={()=> activeTab = tab.id} class:selected={activeTab === tab.id} class="rounded-sm bg-white px-5 py-2 w-auto m-5 mr-0 cursor-pointer hover:inset-shadow-sm hover:text-gray-600">{tab.label}</button>
    {/each}
</nav>
<br>
{#if showPopupLayer}
<div class="fixed inset-0 bg-black/[80%] flex justify-center items-center z-50">
    <ImportPopup on:close={handlePopupClose} />
</div>
{/if}
{#if !SelectedSquadID}
    <p class="text-gray-500">Select squad to begin</p>
{:else}
<svelte:component this={ActiveComponent} on:open={handlePopupOpen}/>
{/if}