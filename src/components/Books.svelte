<script>
  import { onDestroy, onMount } from "svelte";

  let works = [];
  let loading = false;
  let offset = 0;
  let pageSize = 10;

  const handleScroll = () => {
    const { scrollTop, scrollHeight, clientHeight } = document.documentElement;

    if (scrollTop + clientHeight >= scrollHeight - 100 && !loading) {
      fetchData();
    }
  };

  const fetchData = async (firstLoad = false) => {
    loading = true;
    const offsetUsed = firstLoad ? 0 : offset++;
    const pageSizeUsed = firstLoad ? 10 : pageSize + 10;

    try {
      // const response = await fetch("https://randomuser.me/api/");
      const response = await fetch(
        `https://openlibrary.org/subjects/love.json?offset=${offsetUsed}&limit=${pageSizeUsed}`
      );
      const data = await response.json();
      works = data.works;
      loading = false;
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
  {#each works as workItem}
    <div class="book-title">
      <h1>{workItem.title} = {workItem.authors[0].name}</h1>
      <p>{workItem.first_publish_year}</p>
    </div>
  {/each}
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
</style>
