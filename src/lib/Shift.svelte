<script>
  export let onMoveUp;
  export let onMoveDown;
</script>

<div class="shift">
  <button on:click={onMoveUp}>▲</button>
  <button on:click={onMoveDown}>▼</button>
</div>

<style>
  .shift {
    display: flex;
    flex-direction: column;
  }
  button {
    padding: 0;
    padding-left: 8px;
    padding-right: 8px;
    font-size: 12px;
  }
</style>
