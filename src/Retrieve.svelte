<script>
  import Button, { Label } from "@smui/button";
  import Textfield from "@smui/textfield";
  import Icon from "@smui/textfield/icon";
  import HelperText from "@smui/textfield/helper-text";
  import CopyClipBoard from "./CopyClipBoard.svelte";

  let generatedPass = "";
  let finalPass = "";
  let finalPassRetrieved = false;

  $: startBefore1 = 0;
  $: if (startBefore1 < 0) {
    startBefore1 = 0;
  }
  $: numOfChar1 = 0;
  $: if (numOfChar1 < 0) {
    numOfChar1 = 0;
  }
  $: startBefore2 = 0;
  $: if (startBefore2 < 0) {
    startBefore2 = 0;
  }
  $: numOfChar2 = 0;
  $: if (numOfChar2 < 0) {
    numOfChar2 = 0;
  }

  function removeMultipleSubstringsAtIndex(str, substrings) {
    // Create a copy of the original string
    let newStr = str;

    // Iterate through the array of substrings and lengths
    for (const { length, index } of substrings) {
      // Use the slice() method to split the string into three parts:
      // the first part is the substring up to the specified index,
      // the second part is the substring to be removed,
      // and the third part is the rest of the original string
      const firstPart = newStr.slice(0, index);
      const secondPart = newStr.slice(index, index + length);
      const thirdPart = newStr.slice(index + length);

      // Use the replace() method to replace the second part with an empty string
      newStr = firstPart.concat(thirdPart).replace(secondPart, "");
    }

    // Return the modified string
    return newStr;
  }

  function retriveFinalPass() {
    const _finalPass = removeMultipleSubstringsAtIndex(generatedPass, [
      { length: numOfChar1, index: startBefore1 },
      { length: numOfChar2, index: startBefore2 },
    ]);
    finalPass = _finalPass;
    finalPassRetrieved = true;
    console.log(_finalPass);
  }

  const copy = () => {
    const app = new CopyClipBoard({
      target: document.getElementById("clipboard"),
      props: { finalPass },
    });
    app.$destroy();
  };
</script>

<div class="generatedPass__pin__container">
  <div>
    <Textfield
      class="shaped-filled"
      variant="outlined"
      bind:value={generatedPass}
      label="Generated Password"
    >
      <Icon class="material-icons" slot="leadingIcon">password</Icon>
    </Textfield>
  </div>
  <div class="pin_container">
    <span>PIN</span>
    <Textfield
      style="width: 80px; margin: 2px 8px;"
      variant="outlined"
      type="number"
      bind:value={startBefore1}
    />
    <Textfield
      style="width: 80px; margin: 2px 8px;"
      variant="outlined"
      type="number"
      bind:value={numOfChar1}
    />
    <Textfield
      style="width: 80px; margin: 2px 8px;"
      variant="outlined"
      type="number"
      bind:value={startBefore2}
    />
    <Textfield
      style="width: 80px; margin: 2px 8px;"
      variant="outlined"
      type="number"
      bind:value={numOfChar2}
    />
  </div>

  <div>
    <Button
      on:click={retriveFinalPass}
      variant="raised"
      disabled={generatedPass === ""}
    >
      <Label>Retrieve Actual Password</Label>
    </Button>
  </div>
</div>

{#if finalPassRetrieved}
  <div class="final_pass_container">
    <div>
      <span>Retrieved Actual/Pseudo Password:</span>
      <Textfield variant="outlined" bind:value={finalPass} />
      &nbsp; <Button on:click={copy}>
        <Icon class="material-icons">content_copy</Icon>
        <Label>copy</Label>
      </Button>
      <div id="clipboard" />
    </div>
  </div>
{/if}

<style>
  .generatedPass__pin__container {
    display: flex;
    flex-direction: column;
    text-align: center;
    padding: 20px;
    box-shadow: rgb(0 0 0 / 5%) 0px 6px 24px 0px,
      rgb(0 0 0 / 8%) 0px 0px 0px 1px;
  }
  .generatedPass__pin__container > div {
    margin: 20px;
  }
  .pin_container span {
    display: block;
    font-weight: 700;
    color: #6200ee;
    padding: 5px;
  }

  .final_pass_container {
    margin: 20px;
    display: flex;
    align-items: center;
    justify-content: center;
    padding: 20px;
    border-radius: 4px;
    box-shadow: rgb(0 0 0 / 16%) 0px 10px 36px 0px,
      rgb(0 0 0 / 6%) 0px 0px 0px 1px;
  }

  .final_pass_container > div > span {
    font-weight: 700;
    color: #6200ee;
    margin-right: 5px;
  }
</style>
