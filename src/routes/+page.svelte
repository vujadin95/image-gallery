<script lang="ts">
  import { onMount } from "svelte";
  import { fade, fly } from "svelte/transition";
  import { PUBLIC_ACCESS_KEY } from "$env/static/public";
  let searchedText: string = "";

  interface Images {
    id: string;
    alt_description: string;
    urls: { regular: string };
    user: { name: string; links: { html: string } };
  }

  //user: {first_name:string; last_name:string}
  let images: Images[] = [];

  const getImages = async () => {
    const response = await fetch(
      `https://api.unsplash.com/search/photos?page=1&query=${
        searchedText || "universe"
      }&client_id=${PUBLIC_ACCESS_KEY}`
    );
    const data = await response.json();
    images = data.results;
    console.log(images);
  };
  onMount(() => {
    getImages();
  });
  const handleSearch = () => {
    if (!searchedText) return;
    getImages();
    searchedText = "";
  };
</script>

<main>
  <h1 class="title">Welcome to my Image Gallery</h1>
  <div class="search-container">
    <input
      bind:value={searchedText}
      class="input"
      type="text"
      placeholder="Enter desired images..."
      name="search"
      id="search"
    />

    <button on:click={handleSearch} class="search-btn">Search</button>
  </div>
  <div class="images-container">
    {#each images as image, i (image.id)}
      <div
        class="image-wrapper"
        in:fly={{ y: 200, duration: 2000, delay: i * 250 }}
        out:fade
      >
        <img
          src={image.urls.regular}
          alt={image.alt_description}
          class="image"
          loading={"lazy"}
        />
        <p class="author">
          {image.user.name}
        </p>
        <a class="visit-user" href={image.user.links.html} target="_blank"
          >Profile</a
        >
      </div>
    {/each}
  </div>
</main>

<style>
  @import url("https://fonts.googleapis.com/css2?family=Poppins:wght@400;500;600;700&display=swap");

  :global(*) {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
  }
  :global(html) {
    font-family: "Poppins", sans-serif;
    scroll-behavior: smooth;
  }
  :global(body) {
    background-color: #333333;
  }
  main {
    max-width: 1230px;
    margin: 0 auto;
    text-align: center;
    padding: 20px;
  }
  .title {
    margin-bottom: 15px;
    color: #ede8eb;
    letter-spacing: 1.2px;
  }
  .search-container {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    gap: 15px;
    max-width: 100%;
  }
  .input {
    max-width: 300px;
    background-color: #eee;
    border: none;
    padding: 10px;
    font-size: 18px;
    border-radius: 4px;
    font-family: inherit;
    flex-grow: 1;
    outline: none;
  }
  .input:focus {
    outline: 1px solid red;
    color: #333333;
  }
  .search-btn {
    font-family: inherit;
    font-size: 18px;
    font-weight: 600;
    border: none;
    color: #333333;
    cursor: pointer;
    background-color: #ede8eb;
    border-radius: 4px;
    border-style: none;
    outline: none;
    padding: 9px 20px;
    user-select: none;
    -webkit-user-select: none;
    user-select: none;
    touch-action: manipulation;
    transition: background-color 200ms ease;
  }
  .search-btn:hover,
  .search-btn:focus {
    background-color: rgba(218, 165, 32, 0.705);
    color: #ede8eb;
  }
  .search-btn:active {
    transform: scale(1.05);
  }
  .images-container {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    gap: 15px;
    margin-top: 20px;
    position: relative;
  }
  .image-wrapper {
    position: relative;
    display: flex;
    width: 370px;
    height: 250px;
    border-radius: 5px;
    filter: drop-shadow(-1px -2px 8px #000000);
    overflow: hidden;
  }
  .image {
    width: 100%;
    border-radius: 5px;
    object-fit: cover;
    object-position: center;
    transition: transform 250ms ease-in;
    -webkit-user-select: none;
    user-select: none;
  }
  .image-wrapper:hover .image {
    transform: scale(1.05);
  }

  .author {
    position: absolute;
    top: 10px;
    left: 10px;
    z-index: 1;
    color: white;
    background-color: rgba(218, 165, 32, 0.5);
    padding: 4px 8px;
    border-radius: 2px;
  }
  .visit-user,
  .visit-user:visited {
    position: absolute;
    bottom: 10px;
    right: 10px;
    color: white;
    text-decoration: none;
    background-color: #222222a9;
    padding: 4px 8px;
    border-radius: 2px;
    transition: background-color 200ms ease;
    z-index: 1;
  }

  .visit-user:hover {
    background-color: goldenrod;
    outline: none;
  }
</style>
