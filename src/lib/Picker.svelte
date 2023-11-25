<script>
  import tv from "$lib/assets/tv.svg";
  export let options = [];
  export let onPick;

  let picked = "";
  let picking = false;

  const pick = (option) => {
    picking = false;
    picked = option;
    onPick(option);
  };
</script>

<div class="picker">
  <button class="pick_btn" on:click={() => (picking = !picking)}>
    {#if picked}
      <img src={picked.icon} alt={picked.name} />
    {:else}
      <img src={tv} alt="?" />
    {/if}
  </button>
  {#if picking}
    <div class="picker_options {picking}">
      {#each options as option}
        <button id={options} class="option" on:click={() => pick(option)}
          ><img src={option.icon} alt={option.name} />{option.name}</button
        >
      {/each}
    </div>
  {/if}
</div>

<style>
  button {
    padding: 2px;
    width: 100%;
    min-width: 28px;
    height: 28px;
    text-wrap: nowrap;
    justify-content: flex-start;
  }
  .pick_btn {
    justify-content: center;
  }

  .picker {
    position: relative;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
  }

  .picker_options {
    position: absolute;
    display: flex;
    flex-direction: column;
    top: calc(100% + 4px);
    padding: 4px;
    gap: 2px;
    background-color: rgb(10, 10, 10);
    border: 1px solid rgb(25, 25, 25);
    transform-origin: top;
    animation: scaleInY 0.2s ease;
  }
  .picker_options.false {
    transform: scaleY(0);
  }
  .picker_options.true {
    transform: scaleY(1);
  }
</style>
