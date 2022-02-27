<script lang="ts">
  import Clipboard from "$lib/icons/Clipboard.svelte";
  const clip = (content: string) => {
    navigator.clipboard.writeText(content);
  };

  const splitThread = (input: string) => {
    if (!input || input.length <= 1) {
      return [];
    }
    let sentences = input.split(/[.!?]/);
    let tweets = [];
    let currentTweet = "";
    for (let sentence of sentences) {
      // case 1: sentence is too long
      if (sentence.length > 280) {
        // TODO do better error handling
        currentTweet = "Error: sentence too long";
        break;
      }
      // case 2: sentence + current tweet is too long, ship current tweet
      if (sentence.length + currentTweet.length >= 280) {
        tweets.push(currentTweet);
        currentTweet = "";
      }
      // case 3: sentence will fit in current tweet
      currentTweet += sentence;
    }
    // push last tweet that was composed
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
      <div class="shadow-md rounded-md max-h-content">
        <p class="p-2">{tweet}</p>
        <div class="grid justify-end p-2" on:click={() => clip(tweet)}>
          <Clipboard />
        </div>
      </div>
    {/each}
  </div>
</div>
