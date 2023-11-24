<script>
  import Picker from "../lib/Picker.svelte";
  import Shift from "../lib/Shift.svelte";

  let streams = [{ type: "Twitch", channel: "quin69" }];

  let showStreams = false;
  let streamType = "";
  let streamChannel = "";

  const addStream = () => {
    if (streamType && streamChannel) {
      streams = [...streams, { type: streamType, channel: streamChannel }];
      streamChannel = "";
    }
  };

  const removeStream = (stream) => {
    streams = streams.filter(
      (s) => !(s.type === stream.type && s.channel === stream.channel)
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
</script>

<div class="main">
  <div class="options_container">
    <button on:click={() => (showStreams = !showStreams)}>≡</button>
    <div class="options {showStreams}">
      {#each streams as stream, idx}
        <button class="minus_btn" on:click={() => removeStream(stream)}
          >-</button
        >
        <p>{stream.type}</p>
        <input type="text" bind:value={streams[idx].channel} />
        <Shift
          onMoveUp={() => moveStream(idx, idx - 1)}
          onMoveDown={() => moveStream(idx, idx + 1)}
        />
      {/each}
      <button class="add_btn" on:click={addStream}>+</button>
      <Picker
        options={["Twitch", "Rumble"]}
        onPick={(pick) => (streamType = pick)}
      />
      <input
        type="text"
        bind:value={streamChannel}
        on:keypress={(e) => onInputEnter(e)}
      />
    </div>
  </div>
  {#each streams as stream}
    {#if stream.type === "Twitch"}
      <iframe
        title="twitch"
        src="https://player.twitch.tv/?autoplay=true&muted=false&channel={stream.channel}&parent=localhost&parent=multi-stream-livid.vercel.app"
        frameborder="0"
        allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture; fullscreen"
      ></iframe>
    {:else if stream.type === "Rumble"}
      <iframe
        title="rumble"
        src="{stream.channel}&autoplay=2"
        frameborder="0"
        allowfullscreen
      ></iframe>
    {/if}
  {/each}
</div>

<style>
  .main {
    position: absolute;
    display: grid;
    grid-template-columns: 1fr;
    width: 100%;
    height: 100%;
    color: white;
    background-color: black;
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
    left: 0;
    top: 0;
    color: white;
    background-color: rgb(10, 10, 10);
    border: 1px solid rgb(50, 50, 50);
  }

  .option {
    color: white;
    background-color: black;
  }
  .options.true {
    display: grid;
    padding: 8px;
    grid-template-columns: repeat(4, min-content);
    gap: 4px;
    align-items: center;
    justify-content: center;
  }
  .options.false {
    display: none;
  }
</style>
