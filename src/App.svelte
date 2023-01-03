<script>
  import Generate from "./Generate.svelte";
  import { SvelteToast } from "@zerodevx/svelte-toast";
  import Tab, { Icon, Label } from "@smui/tab";
  import TabBar from "@smui/tab-bar";
  import Retrieve from "./Retrieve.svelte";
  import Collection from "./Collection.svelte";
  $: savedData = [];

  let tabs = [
    {
      icon: "lock",
      label: "Generate",
    },
    {
      icon: "lock_open",
      label: "Retrieve",
    },
    {
      icon: "list_alt",
      label: "Collection",
    },
  ];

  let active = tabs[0];

  function handleUpdateListOfPass(event) {
    const data = event.detail.data;
    savedData = [...data];
  }
</script>

<div>
  <TabBar {tabs} let:tab bind:active>
    <!-- Note: the `tab` property is required! -->
    <Tab {tab} stacked={true} indicatorSpanOnlyContent={true}>
      <Icon class="material-icons">{tab.icon}</Icon>
      <Label>{tab.label}</Label>
    </Tab>
  </TabBar>
  <!-- <pre class="status">Selected: {active.label}</pre> -->
</div>

<div class="container">
  {#if active.label === "Generate"}
    <Generate {savedData} on:updateListOfPass={handleUpdateListOfPass} />
  {:else if active.label === "Collection"}
    <Collection {savedData} />
  {:else}
    <Retrieve />
  {/if}
</div>

<SvelteToast />

<style>
  .container {
    margin: 20px;
  }
</style>
