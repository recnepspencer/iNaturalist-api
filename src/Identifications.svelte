<script>
    export let identifications = [];
</script>

{#if identifications.length > 0}
    <div class="identifications-container">
        {#each identifications as identification}
            {#if identification.taxon}
                <figure class="identification">
                    <img src={identification.taxon.default_photo ? identification.taxon.default_photo.square_url : 'https://via.placeholder.com/300'} alt={identification.taxon.name}>
                    <figcaption>
                        <h2>{identification.taxon.name}</h2>
                        <p>Observed on: {new Date(identification.observed_on).toLocaleDateString()}</p>
                        {#if identification.taxon.ancestors && identification.taxon.ancestors.length > 0}
                            <p>Related Species:</p>
                            <ul>
                                {#each identification.taxon.ancestors as ancestor}
                                    <li>{ancestor.name}</li>
                                {/each}
                            </ul>
                        {/if}
                    </figcaption>
                </figure>
            {/if}
        {/each}
    </div>
{:else}
    <p>No identifications found.</p>
{/if}

<style>
    .identifications-container {
        display: flex;
        flex-wrap: wrap;
        gap: 20px;
        justify-content: center;
    }
    .identification {
        background: linear-gradient(135deg, #f8f9fa, #e9ecef);
        border: 1px solid #dee2e6;
        border-radius: 10px;
        box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
        padding: 20px;
        margin: 10px;
        text-align: center;
        width: 300px; /* Standardized width */
        height: 400px; /* Standardized height */
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: space-between;
        transition: transform 0.2s ease-in-out, box-shadow 0.2s ease-in-out;
    }
    .identification:hover {
        transform: translateY(-5px);
        box-shadow: 0 8px 16px rgba(0, 0, 0, 0.2);
    }
    .identification img {
        border-radius: 10px;
        width: 100%; /* Ensure the image takes full width of the figure */
        height: auto;
        max-height: 200px; /* Limit the height of the image */
        object-fit: cover;
    }
    .identification figcaption {
        margin-top: 10px;
    }
    .identification h2 {
        font-size: 1.2em;
        margin: 10px 0;
        color: #495057;
    }
    .identification p {
        margin: 5px 0;
        font-size: 0.9em;
        color: #6c757d;
    }
    .identification ul {
        list-style-type: none;
        padding: 0;
        margin: 10px 0 0;
    }
    .identification li {
        font-size: 0.8em;
        color: #6c757d;
    }
</style>
