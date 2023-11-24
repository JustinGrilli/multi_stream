<script>
  export let options = [];
  export let onPick;

  let picked = "--";
  let picking = false;

  const pick = (option) => {
    picking = false;
    picked = option;
    onPick(option);
  };
</script>

<div class="picker">
  <button on:click={() => (picking = !picking)}>{picked}</button>
  <div class="options {picking}">
    {#each options as option}
      <button id={options} class="option" on:click={() => pick(option)}
        >{option}</button
      >
    {/each}
  </div>
</div>

<style>
  button {
    height: 100%;
    width: 100%;
  }
  .picker {
    position: relative;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    min-width: 4ch;
    min-height: 2ch;
  }
  .options {
    position: absolute;
    top: calc(100% + 4px);
    padding: 4px;
    gap: 2px;
    background-color: rgb(10, 10, 10);
    border: 1px solid rgb(25, 25, 25);
  }
  .options.false {
    display: none;
  }
  .options.true {
    display: flex;
    flex-direction: column;
  }
</style>
