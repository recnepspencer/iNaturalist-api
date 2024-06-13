<script>
  import SearchForm from './SearchForm.svelte';
  import Identifications from './Identifications.svelte';

  let identifications = [];

  function handleIdentifications(event) {
      identifications = event.detail.identifications;
  }
</script>

<main>
  <h1>Search for Places and Identifications</h1>
  <SearchForm on:identifications={handleIdentifications} />
  <Identifications {identifications} />
</main>

<style>

  main {
      padding: 1rem;
      max-width: 800px;
      margin: auto;

  }
  h1 {
      text-align: center;
      font-size: 2em;
      margin-bottom: 20px;
      color: #343a40;
  }
</style>
