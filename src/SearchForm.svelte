<script>
    import { createEventDispatcher } from 'svelte';
    import axios from 'axios';
    let place = '';
    let places = [];
    let selectedPlaceId = null;
    let startDate = '';
    let endDate = '';
    let isLoading = false;  // Add this line to track the loading state

    const dispatch = createEventDispatcher();

    async function searchPlace() {
        try {
            const response = await axios.get(`https://api.inaturalist.org/v1/places/autocomplete?q=${place}`);
            places = response.data.results;
        } catch (error) {
            console.error(error);
            places = [];
        }
    }

    async function fetchIdentifications() {
        if (!selectedPlaceId) return;

        isLoading = true;  // Start loading
        try {
            const identificationsResponse = await axios.get(`https://api.inaturalist.org/v1/observations?place_id=${selectedPlaceId}&d1=${startDate}&d2=${endDate}`);
            let identifications = identificationsResponse.data.results;

            // Remove duplicates
            const seenTaxa = new Set();
            identifications = identifications.filter(identification => {
                if (!identification.taxon || seenTaxa.has(identification.taxon.id)) {
                    return false;
                }
                seenTaxa.add(identification.taxon.id);
                return true;
            });

            // Sort by name
            identifications.sort((a, b) => a.taxon.name.localeCompare(b.taxon.name));

            dispatch('identifications', { identifications });
        } catch (error) {
            console.error(error);
            dispatch('identifications', { identifications: [] });
        } finally {
            isLoading = false;  // Stop loading
        }
    }

    function handlePlaceSelection(placeId) {
        selectedPlaceId = placeId;
    }
</script>

<form on:submit|preventDefault={fetchIdentifications}>
    <label for="place">Enter Place:</label>
    <input type="text" id="place" bind:value={place} on:input={searchPlace} required>

    <label for="startDate">Start Date:</label>
    <input type="date" id="startDate" bind:value={startDate}>

    <label for="endDate">End Date:</label>
    <input type="date" id="endDate" bind:value={endDate}>

    <button type="submit" disabled={!selectedPlaceId}>Search</button>
</form>

<ul>
    {#each places as place}
        <li>
            <button type="button" on:click={() => handlePlaceSelection(place.id)}>
                {place.display_name}
            </button>
        </li>
    {/each}
</ul>

{#if isLoading}
    <div class="spinner-container">
        <div class="loading">Loading</div>
        <div class="spinner"></div>
    </div>
{/if}

<style>
    form {
        display: flex;
        flex-direction: column;
        gap: 10px;
        margin-bottom: 20px;
        align-items: center;
    }
    label {
        font-weight: bold;
        margin-bottom: 5px;
        color: #495057;
    }
    input {
        padding: 10px;
        border: 1px solid #ced4da;
        border-radius: 5px;
        font-size: 1em;
    }
    button {
        background-color: #007bff;
        color: rgb(0, 0, 0);
        border: none;
        padding: 10px 20px;
        border-radius: 5px;
        cursor: pointer;
        font-size: 1em;
        transition: background-color 0.2s ease-in-out;
    }
    button:disabled {
        background-color: #6c757d;
        cursor: not-allowed;
    }
    button:hover:not(:disabled) {
        background-color: #0056b3;
    }
    ul {
        list-style-type: none;
        padding: 0;
        margin: 0;
        display: flex;
        flex-wrap: wrap;
        gap: 10px;
        justify-content: center;
    }
    li {
        margin: 0;
    }
    li button {
        padding: 10px 20px;
        border-radius: 5px;
        background-color: #f8f9fa;
        border: 1px solid #ced4da;
        cursor: pointer;
        transition: background-color 0.2s ease-in-out;
    }
    li button:hover {
        background-color: #e2e6ea;
    }
    .spinner {
    border: 4px solid rgba(0, 0, 0, 0.1);
    width: 36px;
    height: 36px;
    border-radius: 50%;
    border-left-color: #007bff;
    animation: spin 1s linear infinite;
    margin-left: auto;
    margin-right: auto;
    margin-top: 10px;
    margin-bottom: 30px;
}
.loading{
    text-align: center;
    font-size: 1.5em;
    color: #495057;
    margin-top: 20px;
}

@keyframes spin {
    0% {
        transform: rotate(0deg);
    }
    100% {
        transform: rotate(360deg);
    }
}
</style>
