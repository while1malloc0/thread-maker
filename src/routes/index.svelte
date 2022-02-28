<script lang="ts">
  import Clipboard from "$lib/icons/Clipboard.svelte";
  import ArrowRight from "$lib/icons/ArrowRight.svelte";

  const clip = (content: string) => {
    navigator.clipboard.writeText(content);
  };

  let input;
  let state: { tweets?: string[]; errors?: any } = { tweets: [], errors: {} };
  $: textAreaBorder = state.errors?.banner ? "border-red-500" : "border-gray-500";

  const splitThread = (input: string) => {
    if (!input || input.length <= 1) {
      return { tweets: [] };
    }
    let sentences = input.split(/([.!?])/);
    let tweets = [];
    let currentTweet = "";
    for (let sentence of sentences) {
      // case 1: sentence is too long
      if (sentence.length > 280) {
        return {
          errors: {
            banner: `Error: the sentence ${sentence.slice(0, 30)}... is too long to fit into a Tweet.`,
          },
          tweets: state.tweets,
        };
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
    return { tweets: tweets };
  };
</script>

<div>
  {#if state.errors?.banner}
    <div class="bg-red-500 text-white text-center p-2">{state.errors.banner}</div>
  {/if}
</div>
<h1 class="px-5 py-2 text-3xl">Thread maker</h1>
<div class="p-5 grid grid-flow-col grid-cols-4">
  <div class="col-span-2 p-2 border-2 {textAreaBorder} rounded-md">
    <textarea
      on:keyup={() => (state = splitThread(input))}
      bind:value={input}
      class="w-full h-screen"
      placeholder="Long text goes in here..."
    />
  </div>
  <div class="grid items-center justify-center"><ArrowRight classes="w-40 h-40" /></div>
  <div class="col-span-1 p-2 grid grid-flow-row border-2 border-gray-500 rounded-md">
    {#if state.tweets.length < 1}
      <div class="shadow-md rounded-md"><p class="p-2">...and comes out as a thread here</p></div>
    {/if}
    {#each state.tweets as tweet}
      <div class="shadow-md rounded-md max-h-content border-1 border-gray-100">
        <p class="p-2">{tweet}</p>
        <div class="grid justify-end p-2" on:click={() => clip(tweet)}>
          <Clipboard />
        </div>
      </div>
    {/each}
  </div>
</div>
