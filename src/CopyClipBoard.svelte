<script>
  import { onMount } from "svelte";

  export let finalPass;

  let textarea;

  onMount(() => {
    textarea.select();
    document.execCommand("copy");
  });
</script>

<textarea bind:value={finalPass} bind:this={textarea} />
