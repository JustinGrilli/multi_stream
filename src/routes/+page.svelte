<script>
  import twitch from "$lib/assets/twitch.png";
  import rumble from "$lib/assets/rumble.svg";
  import kick from "$lib/assets/kick.jpg";
  import Picker from "../lib/Picker.svelte";
  import Shift from "../lib/Shift.svelte";
  let twitchParents = "&parent=localhost&parent=multi-cross-stream.vercel.app";
  let streams = [];
  let columns = 1;

  let streamTypes = [
    { name: "Twitch", icon: twitch },
    { name: "Rumble", icon: rumble },
    { name: "Kick", icon: kick },
  ];
  let showStreams = false;
  let streamType = "";
  let streamChannel = "";

  const addStream = () => {
    const existing = streams.filter(
      (s) =>
        s.type.name.toLowerCase() === streamType.name?.toLowerCase() &&
        s.channel.toLowerCase() === streamChannel?.toLowerCase()
    ).length;
    if (streamType && streamChannel && existing === 0) {
      streams = [...streams, { type: streamType, channel: streamChannel }];
      streamChannel = "";
    }
  };

  const removeStream = (stream) => {
    streams = streams.filter(
      (s) => !(s.type.name === stream.type.name && s.channel === stream.channel)
    );
  };

  const moveStream = (fromIndex, toIndex) => {
    const newStreams = [...streams];
    newStreams.splice(toIndex, 0, newStreams.splice(fromIndex, 1)[0]);
    streams = [...newStreams];
  };

  const onInputEnter = (event) => {
    if (event.key === "Enter") {
      addStream();
    }
  };

  $: {
    if (streams.length < 3) {
      columns = 1;
    } else {
      columns = 2;
    }
  }
</script>

<div class="main" style="grid-template-columns: repeat({columns}, 1fr);">
  {#if streams.length === 0}
    <h2 style="text-align: center; user-select: none;">
      Click the ≡ button in the top left corner to add streams!
    </h2>
  {/if}
  <div class="options_container">
    <button class="streams_btn" on:click={() => (showStreams = !showStreams)}
      >≡</button
    >
    <div class="options {showStreams}">
      {#each streams as stream, idx}
        <button class="minus_btn" on:click={() => removeStream(stream)}
          >-</button
        >
        <img src={stream.type.icon} alt={stream.type.name} />
        <input type="text" bind:value={streams[idx].channel} />
        <Shift
          onMoveUp={() => moveStream(idx, idx - 1)}
          onMoveDown={() => moveStream(idx, idx + 1)}
        />
      {/each}
      <button class="add_btn" on:click={addStream}>+</button>
      <Picker options={streamTypes} onPick={(pick) => (streamType = pick)} />
      <input
        type="text"
        bind:value={streamChannel}
        on:keypress={(e) => onInputEnter(e)}
      />
    </div>
  </div>
  {#each streams as stream}
    {#if stream.type.name === "Twitch"}
      <iframe
        title="twitch"
        src="https://player.twitch.tv/?autoplay=true&muted=false&channel={stream.channel}{twitchParents}"
        frameborder="0"
        allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture; fullscreen"
      ></iframe>
    {:else if stream.type.name === "Rumble"}
      <iframe
        title="rumble"
        src="{stream.channel}&autoplay=2"
        frameborder="0"
        allowfullscreen
      ></iframe>
    {:else if stream.type.name === "Kick"}
      <iframe
        title="kick"
        src="https://player.kick.com/{stream.channel}"
        frameborder="0"
        scrolling="no"
        allowfullscreen="true"
      >
      </iframe>
    {/if}
  {/each}
</div>

<style>
  .main {
    position: absolute;
    display: grid;
    width: 100%;
    height: 100%;
    color: white;
    background-color: black;
  }

  .streams_btn {
    font-size: 20px;
  }

  .add_btn,
  .minus_btn {
    width: 20px;
    height: 20px;
    font-weight: 900;
    border-radius: 50%;
  }
  .add_btn {
    background-color: rgb(0, 88, 0);
  }
  .add_btn:hover {
    background-color: green;
  }
  .minus_btn {
    background-color: rgb(95, 0, 0);
  }
  .minus_btn:hover {
    background-color: rgb(128, 0, 0);
  }

  iframe {
    width: 100%;
    height: 100%;
  }

  .options_container {
    position: absolute;
    display: flex;
    flex-direction: column;
    align-items: flex-start;
    top: 10px;
    left: 10px;
  }

  .options {
    position: absolute;
    left: 0;
    top: 100%;
    padding: 8px;
    display: grid;
    grid-template-columns: repeat(4, min-content);
    gap: 4px;
    align-items: center;
    align-content: center;
    justify-content: center;
    justify-items: center;
    transition: transform 0.1s ease;
    color: white;
    background-color: rgb(10, 10, 10);
    border: 1px solid rgb(50, 50, 50);
    transform-origin: top left;
  }
  .options.true {
    transform: scale(1);
  }
  .options.false {
    transform: scale(0);
  }

  .option {
    color: white;
    background-color: black;
  }
</style>
