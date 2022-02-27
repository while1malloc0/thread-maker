<script lang="ts">
  const splitThread = (input: string) => {
    if (!input || input.length <= 1) {
      return [];
    }
    let sentences = input.split(/[.!?]/);
    let tweets = [];
    let currentTweet = "";
    for (let sentence of sentences) {
      if (currentTweet.length >= 280) {
        tweets.push(currentTweet);
        currentTweet = "";
      }
      if (sentence.length > 280) {
      } else {
        currentTweet += sentence;
      }
    }
    tweets.push(currentTweet);
    return tweets;
  };

  let input;
  let tweets = [];
  $: {
    tweets = splitThread(input);
    tweets = tweets;
  }
</script>

<div class="p-5 grid grid-flow-col grid-cols-3">
  <div class="col-span-2 p-2 border-2 border-gray-500 rounded-md">
    <textarea bind:value={input} class="w-full h-screen" placeholder="Start typing your thread here..." />
  </div>
  <div class="col-span-1 p-2 grid grid-flow-row">
    {#if tweets.length < 1}
      <div class="shadow-md rounded-md"><p class="p-2">And it'll appear split up here.</p></div>
    {/if}
    {#each tweets as tweet}
      <div class="shadow-md rounded-md"><p class="p-2">{tweet}</p></div>
    {/each}
  </div>
</div>
