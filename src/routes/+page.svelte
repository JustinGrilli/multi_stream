<script>
  import menu from "$lib/assets/menu.svg";
  import twitch from "$lib/assets/twitch.png";
  import rumble from "$lib/assets/rumble.svg";
  import kick from "$lib/assets/kick.jpg";
  import Picker from "../lib/Picker.svelte";
  import Shift from "../lib/Shift.svelte";
  import Video from "../lib/Video.svelte";
  import { onMount } from "svelte";
  let twitchParents = "&parent=localhost&parent=multi-cross-stream.vercel.app";
  let streams = [];
  let columns = 1;
  let platforms = [
    { name: "Twitch", icon: twitch, identifier: "Channel Name" },
    { name: "Rumble", icon: rumble, identifier: "Embed IFRAME URL" },
    { name: "Kick", icon: kick, identifier: "Channel Name" },
  ];
  let innerWidth;
  let innerHeight;
  let showStreams = false;
  let streamPlatform = platforms[0];
  let streamChannel = "";

  const addStream = () => {
    const existing = streams.filter(
      (s) =>
        s.platform.name.toLowerCase() === streamPlatform.name?.toLowerCase() &&
        s.channel.toLowerCase() === streamChannel?.toLowerCase()
    ).length;
    if (streamPlatform && streamChannel && existing === 0) {
      streams = [
        ...streams,
        { platform: streamPlatform, channel: streamChannel },
      ];
      streamChannel = "";
    }
  };

  const removeStream = (stream) => {
    streams = streams.filter(
      (s) =>
        !(
          s.platform.name === stream.platform.name &&
          s.channel === stream.channel
        )
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

  const setColumns = (streamsLength, width, height) => {
    // Update the number of column in the main grid,
    // based on the number of streams and width/height of the window
    if (!width || !height) return;

    // Landscape
    if (width > height) {
      if (streamsLength < 3) {
        columns = 1;
      } else if (streamsLength < 7) {
        columns = 2;
      } else if (streamsLength < 13) {
        columns = 3;
      } else {
        columns = 4;
      }
    }
    // Portrait
    if (width < height) {
      if (streamsLength < 7) {
        columns = 1;
      } else if (streamsLength < 17) {
        columns = 2;
      } else {
        columns = 3;
      }
    }
  };

  $: setColumns(streams.length, innerWidth, innerHeight);
</script>

<!-- Tracks updates to width / height of the window -->
<svelte:window bind:innerWidth bind:innerHeight />

<div class="main" style="grid-template-columns: repeat({columns}, 1fr);">
  {#if streams.length === 0}
    <h3 style="text-align: center; user-select: none;">
      Click the button in the top left corner to add streams!
    </h3>
  {/if}
  <div class="options_container">
    <button class="streams_btn" on:click={() => (showStreams = !showStreams)}
      ><img src={menu} alt="menu" /></button
    >
    <div class="options {showStreams}">
      {#each streams as stream, idx}
        <button class="minus_btn" on:click={() => removeStream(stream)}
          >-</button
        >
        <img src={stream.platform.icon} alt={stream.platform.name} />
        <input type="text" bind:value={streams[idx].channel} />
        <Shift
          onMoveUp={() => moveStream(idx, idx - 1)}
          onMoveDown={() => moveStream(idx, idx + 1)}
        />
      {/each}
      <button class="add_btn" on:click={addStream}>+</button>
      <Picker options={platforms} onPick={(pick) => (streamPlatform = pick)} />
      <input
        type="text"
        placeholder={streamPlatform.identifier}
        bind:value={streamChannel}
        on:keypress={(e) => onInputEnter(e)}
      />
    </div>
  </div>
  {#each streams as stream}
    <Video
      platformName={stream.platform.name}
      streamChannel={stream.channel}
      {twitchParents}
    />
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
    overflow: hidden;
  }

  .options_container {
    position: absolute;
    display: flex;
    flex-direction: column;
    align-items: flex-start;
    top: 10px;
    left: 10px;
    z-index: 1;
    opacity: 0;
    transition: opacity 0.2s ease;
  }
  .options_container:hover,
  .main:hover > .options_container {
    opacity: 1;
  }

  .streams_btn {
    padding: 2px;
    padding-left: 4px;
    padding-right: 4px;
    font-size: 24px;
  }

  .add_btn,
  .minus_btn {
    width: 22px;
    height: 22px;
    font-weight: 900;
    border-radius: 50%;
  }
  .add_btn {
    background-color: rgb(0, 88, 0);
    border: 1px solid green;
  }
  .add_btn:hover {
    background-color: green;
  }
  .minus_btn {
    background-color: rgb(95, 0, 0);
    border: 1px solid rgb(128, 0, 0);
  }
  .minus_btn:hover {
    background-color: rgb(128, 0, 0);
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
