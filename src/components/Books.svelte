<script>
  import { onDestroy, onMount } from "svelte";

  let works = [];
  let loading = false;
  let firstLoad = true;
  let loadingText = "Chill, your books are loading...";
  let offset = 0;
  let pageSize = 10;

  const handleScroll = () => {
    const { scrollTop, scrollHeight, clientHeight } = document.documentElement;

    if (scrollTop + clientHeight >= scrollHeight - 100 && !loading) {
      fetchData();
    }
  };

  const fetchData = async () => {
    loading = true;
    const offsetUsed = firstLoad ? 0 : offset++;
    const pageSizeUsed = firstLoad ? 10 : pageSize + 10;

    if (!firstLoad) {
      loadingText = "Wow, you are a book worm. Scriptures incoming...";
    }

    try {
      // const response = await fetch("https://randomuser.me/api/");
      const response = await fetch(
        `https://openlibrary.org/subjects/love.json?offset=${offsetUsed}&limit=${pageSizeUsed}`
      );
      const data = await response.json();
      works = data.works;
      loading = false;
      firstLoad = false;
    } catch (error) {
      loading = false;
      console.error("Error fetching user:", error);
    }
  };

  onMount(async () => {
    window.addEventListener("scroll", handleScroll);

    await fetchData(true);
  });

  onDestroy(() => {
    window.removeEventListener("scroll", handleScroll);
  });
</script>

<div>
  {#if loading}
    <div class="splash" class:further-load={!firstLoad}>
      <div>
        <span class="loader"></span>
        <h3>{loadingText}</h3>
      </div>
    </div>
  {:else}
    {#each works as workItem}
      <div class="book-title">
        <h1>{workItem.title} = {workItem.authors[0].name}</h1>
        <p>{workItem.first_publish_year}</p>
      </div>
    {/each}
  {/if}
</div>

<style>
  .book-title {
    text-align: center;
    font-family: "Georgia", serif;
    font-size: 2em;
    color: #333;
    border: 2px solid blue;
    border-radius: 20px;
    padding: 20px;
    margin: 10px;
  }

  .loader {
    width: 48px;
    height: 48px;
    border-radius: 50%;
    display: inline-block;
    position: relative;
    border: 3px solid;
    border-color: #fff #fff transparent transparent;
    box-sizing: border-box;
    animation: rotation 1s linear infinite;
  }
  .loader::after,
  .loader::before {
    content: "";
    box-sizing: border-box;
    position: absolute;
    left: 0;
    right: 0;
    top: 0;
    bottom: 0;
    margin: auto;
    border: 3px solid;
    border-color: transparent transparent #ff3d00 #ff3d00;
    width: 40px;
    height: 40px;
    border-radius: 50%;
    box-sizing: border-box;
    animation: rotationBack 0.5s linear infinite;
    transform-origin: center center;
  }
  .loader::before {
    width: 32px;
    height: 32px;
    border-color: #fff #fff transparent transparent;
    animation: rotation 1.5s linear infinite;
  }

  @keyframes rotation {
    0% {
      transform: rotate(0deg);
    }
    100% {
      transform: rotate(360deg);
    }
  }
  @keyframes rotationBack {
    0% {
      transform: rotate(0deg);
    }
    100% {
      transform: rotate(-360deg);
    }
  }

  .splash {
    height: 100vh;
    width: 100vw;
    top: 0;
    left: 0;
    left: 0;
    right: 0;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .splash > div {
    text-align: center;
    display: flex;
    align-items: center;
    flex-direction: column;
  }

  .further-load {
    top: initial;
    height: 25vh;
  }
</style>
