<script>
  import CopyClipBoard from "./CopyClipBoard.svelte";
  import Button, { Label } from "@smui/button";
  import Textfield from "@smui/textfield";
  import Icon from "@smui/textfield/icon";
  import HelperText from "@smui/textfield/helper-text";
  import { toast } from "@zerodevx/svelte-toast";

  import { createEventDispatcher } from "svelte";

  import { fade } from "svelte/transition";

  export let savedData;

  let actualPass = "D4!";
  let passFor = "";
  let pseudoPass = "";
  // boDdaHatherDerpor4achorjoHoiGeLaM

  $: startBefore1 = 0;
  $: if (startBefore1 < 0) {
    startBefore1 = 0;
  }

  $: startBefore2 = pseudoPass.length - 1;
  $: if (startBefore2 > pseudoPass.length - 1) {
    startBefore2 = pseudoPass.length - 1;
  }

  $: numOfChar1 = 5;
  $: if (numOfChar1 < 5) {
    numOfChar1 = 5;
  }
  $: numOfChar2 = 5;
  $: if (numOfChar2 < 5) {
    numOfChar2 = 5;
  }

  let finalPass = "";
  let isfinalPassGenerated = false;

  const updateSavedData = createEventDispatcher();

  function handleStartBefore1_change(e) {
    startBefore1 = +e.target.value;
    if (startBefore1 === startBefore2) {
      startBefore1 = startBefore1 - 1;
    }
    console.log(startBefore1);
  }
  function handleStartBefore2_change(e) {
    startBefore2 = +e.target.value;
    if (startBefore1 === startBefore2) {
      startBefore2 = startBefore2 + 1;
    }
    console.log(startBefore2);
  }

  function handleNumOfChar1_change(e) {
    numOfChar1 = +e.target.value;
    console.log(numOfChar1);
  }
  function handleNumOfChar2_change(e) {
    numOfChar2 = +e.target.value;
    console.log(numOfChar2);
  }

  function getRandomChars(length) {
    var result = "";
    var characters =
      "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789";
    var charactersLength = characters.length;
    for (var i = 0; i < length; i++) {
      result += characters.charAt(Math.floor(Math.random() * charactersLength));
    }
    return result;
  }

  function addMultipleSubstrings(str, substrings) {
    // Create a copy of the original string
    let newStr = str;

    // Iterate through the array of substrings and lengths
    for (const { sub, length } of substrings) {
      // Use the slice() method to split the string into two parts:
      // the first part is the substring up to the specified length,
      // and the second part is the rest of the original string
      const firstPart = newStr.slice(0, length);
      const secondPart = newStr.slice(length);

      // Use the concat() method to merge the first part, the substring, and the second part
      // into a new string and assign it to newStr
      newStr = firstPart.concat(sub, secondPart);
    }

    // Return the modified string
    return newStr;
  }

  function generate() {
    let first = getRandomChars(numOfChar1);
    let second = getRandomChars(numOfChar2);

    console.log(`First: ${first}`);
    console.log(`Second: ${second}`);

    const _finalPass = addMultipleSubstrings(pseudoPass, [
      { sub: first, length: startBefore1 },
      { sub: second, length: startBefore2 + first.length },
    ]);
    console.log(finalPass);
    finalPass = _finalPass;
    isfinalPassGenerated = true;
  }

  function reset() {
    pseudoPass = "";
  }

  const copy = () => {
    const app = new CopyClipBoard({
      target: document.getElementById("clipboard"),
      props: { finalPass },
    });
    app.$destroy();

    toast.push("Copied!");
  };

  function save_pass_pin() {
    const obj = {
      id: crypto.randomUUID(),
      siteName: passFor,
      layeredPass: finalPass,
      pin: {
        startBefore1: startBefore1,
        numOfChar1: numOfChar1,
        startBefore2: startBefore2,
        numOfChar2: numOfChar2,
      },
    };
    // populate the JSON
    const data = [obj, ...savedData];
    updateSavedData("updateListOfPass", {
      data: data,
    });
    console.log(data);

    // Empty the Site and Pass field
    pseudoPass = "";
    passFor = "";

    toast.push("Saved. Check it in Collection TAB");
  }
</script>

<div class="pseudo_pass_entry_field">
  <div>
    <Textfield
      class="shaped-filled"
      variant="outlined"
      bind:value={passFor}
      label="Site"
    >
      <Icon class="material-icons" slot="leadingIcon">language</Icon>
      <HelperText slot="helper">For which site?</HelperText>
    </Textfield>
  </div>

  <div>
    <Textfield
      class="shaped-filled"
      variant="outlined"
      bind:value={pseudoPass}
      label="password"
    >
      <Icon class="material-icons" slot="leadingIcon">key</Icon>
      <HelperText slot="helper">Use Pseudo, not Actual</HelperText>
    </Textfield>
  </div>
</div>

{#if pseudoPass !== ""}
  <p class="_pseudoPass">
    {#each pseudoPass as passChar, i}
      <span
        class:showStarting1={startBefore1 === i}
        class:showStarting2={startBefore2 === i}
      >
        {passChar}
      </span>
    {/each}
  </p>

  <div class="first">
    <Textfield
      class="pin_field shaped-outlined"
      variant="outlined"
      on:input={handleStartBefore1_change}
      type="number"
      bind:value={startBefore1}
      min="0"
      max={pseudoPass.length - 1}
      label="Start Before character (part 1)"
    />

    <Textfield
      class="pin_field shaped-outlined"
      variant="outlined"
      on:input={handleNumOfChar1_change}
      type="number"
      bind:value={numOfChar1}
      min="5"
      max="35"
      label="Add # number of Characters (for part 1)"
    />
  </div>

  <div class="second">
    <Textfield
      class="pin_field shaped-outlined"
      variant="outlined"
      on:input={handleStartBefore2_change}
      type="number"
      bind:value={startBefore2}
      min="0"
      max={pseudoPass.length - 1}
      label="Start Before character (part 2)"
    />

    <Textfield
      class="pin_field shaped-outlined"
      variant="outlined"
      on:input={handleNumOfChar2_change}
      type="number"
      bind:value={numOfChar2}
      min="5"
      max="35"
      label="Add # number of Characters (for part 2)"
    />
  </div>

  <div class="generate_and_reset">
    <Button on:click={generate} variant="raised" disabled={passFor === ""}>
      <Label>Generate Security Layered Password & Pin Code</Label>
    </Button>
    &nbsp;
    <Button on:click={reset} variant="outlined">
      <Label>Reset</Label>
    </Button>
  </div>

  {#if isfinalPassGenerated}
    <div class="generated_pass_pin" transition:fade>
      <div>
        <span>Security Layered Password:</span>
        <Textfield variant="outlined" bind:value={finalPass} />
        &nbsp; <Button on:click={copy}>
          <Icon class="material-icons">content_copy</Icon>
          <Label>copy</Label>
        </Button>
        <div id="clipboard" />
      </div>

      <div class="pin">
        <span>Pin:</span>
        <div class="pin_container">
          <div>{startBefore1}</div>
          <div>{numOfChar1}</div>
          <div>{startBefore2}</div>
          <div>{numOfChar2}</div>
        </div>
      </div>

      <div>
        <Button on:click={save_pass_pin} variant="raised">
          <Label>Save</Label>
        </Button>
      </div>
    </div>
  {/if}
{/if}

<style>
  .pseudo_pass_entry_field {
    display: flex;
    align-items: center;
    justify-content: center;
  }
  .pseudo_pass_entry_field > div {
    margin: 2px 10px;
  }

  ._pseudoPass {
    font-weight: 500;
    font-size: 1.2em;
    display: flex;
    align-items: center;
    justify-content: center;
    padding: 10px;
    margin: 20px;
    flex-wrap: wrap;
  }
  ._pseudoPass > span {
    margin: 2px;
  }
  .showStarting1::before {
    content: "|";
    width: 1px;
    height: 100%;
    color: #66bb6a;
    background: #66bb6a;
  }
  .showStarting2::before {
    content: "|";
    width: 1px;
    height: 100%;
    color: #29b6f6;
    background: #29b6f6;
  }
  .first,
  .second {
    display: flex;
    align-items: center;
    padding: 20px 10px;
    margin: 20px 0px;
    justify-content: space-between;
    box-shadow: rgba(0, 0, 0, 0.05) 0px 6px 24px 0px,
      rgba(0, 0, 0, 0.08) 0px 0px 0px 1px;
  }

  .generate_and_reset {
    display: flex;
    justify-content: center;
  }

  .generated_pass_pin {
    margin: 20px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    flex-wrap: wrap;

    padding: 20px;
    border-radius: 4px;
    box-shadow: rgb(0 0 0 / 16%) 0px 10px 36px 0px,
      rgb(0 0 0 / 6%) 0px 0px 0px 1px;
  }

  .generated_pass_pin > div {
    margin: 4px;
  }

  .generated_pass_pin > div:nth-child(1),
  .generated_pass_pin > div:nth-child(2) {
    padding: 8px;
    border: 1px dashed #ddd;
  }

  .generated_pass_pin > div > span {
    font-weight: 700;
    color: #6200ee;
    margin-right: 5px;
  }

  .pin {
    display: flex;
    align-items: center;
  }

  .pin .pin_container {
    display: flex;
    align-items: center;
    justify-content: center;
  }
  .pin .pin_container > div {
    padding: 4px;
    margin: 3px;
    box-shadow: rgba(0, 0, 0, 0.05) 0px 6px 24px 0px,
      rgba(0, 0, 0, 0.08) 0px 0px 0px 1px;
    background-color: #eee;
    color: rgb(44, 44, 44);
  }
  .pin .pin_container > div:nth-child(1) {
    background: #66bb6a;
  }
  .pin .pin_container > div:nth-child(3) {
    background: #29b6f6;
  }
</style>
