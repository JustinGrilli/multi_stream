<script>
  export let platformName;
  export let streamChannel;
  export let twitchParents;

  let enhance = true;
</script>

<div class="video-container">
  {#if platformName === "Twitch"}
    <iframe
      title="twitch"
      class={enhance}
      src="https://player.twitch.tv/?autoplay=true&muted=false&channel={streamChannel}{twitchParents}"
      frameborder="0"
      allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture; fullscreen"
    ></iframe>
  {:else if platformName === "Rumble"}
    <iframe
      title="rumble"
      class={enhance}
      src="{streamChannel}&autoplay=2"
      frameborder="0"
      allowfullscreen
    ></iframe>
  {:else if platformName === "Kick"}
    <iframe
      title="kick"
      class={enhance}
      src="https://player.kick.com/{streamChannel}"
      frameborder="0"
      scrolling="no"
      allowfullscreen="true"
    >
    </iframe>
  {/if}
  <button class={enhance} on:click={() => (enhance = !enhance)}>˖✩ ࣪⭑</button>
</div>

<style>
  .video-container {
    position: relative;
    width: 100%;
    height: 100%;
    z-index: 0;
  }

  .video-container > button {
    padding: 0;
    position: absolute;
    top: 10px;
    right: 50%;
    background-color: black;
    opacity: 0;
    transition: opacity 0.2s ease;
    border-width: 0;
    border-style: solid;
  }
  .video-container:hover > button {
    opacity: 0.6;
  }
  .video-container > button:hover {
    opacity: 1;
    border-width: 1px;
  }
  .video-container > button.true {
    border-color: cyan;
    color: cyan;
  }
  .video-container > button.false {
    border-color: grey;
    color: grey;
  }

  .video-container > iframe {
    position: relative;
    width: 100%;
    height: 100%;
  }
  .video-container > iframe.true {
    filter: contrast(1.05);
    image-rendering: crisp-edges;
    image-rendering: pixelated;
  }
</style>
