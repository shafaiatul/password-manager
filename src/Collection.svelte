<script>
  import Button, { Label } from "@smui/button";
  import Textfield from "@smui/textfield";
  import Icon from "@smui/textfield/icon";
  import HelperText from "@smui/textfield/helper-text";

  //import passwords from "./assets/passwords";

  import DataTable, {
    Head,
    Body,
    Row,
    Cell,
    SortValue,
  } from "@smui/data-table";
  import IconButton from "@smui/icon-button";

  export let savedData;

  let items = savedData;
  let sort = "id";
  let sortDirection = "ascending";

  function handleSort() {
    items.sort((a, b) => {
      const [aVal, bVal] = [a[sort], b[sort]][
        sortDirection === "ascending" ? "slice" : "reverse"
      ]();
      if (typeof aVal === "string" && typeof bVal === "string") {
        return aVal.localeCompare(bVal);
      }
      return Number(aVal) - Number(bVal);
    });
    items = items;
  }

  function exportJSON() {
    // Convert the data to a JSON string
    const dataString = JSON.stringify(savedData);

    // Create a blob with the JSON string
    const blob = new Blob([dataString], { type: "application/json" });

    // Create a link element to trigger the download
    const link = document.createElement("a");
    link.href = window.URL.createObjectURL(blob);
    link.download = "data.json";

    // Append the link element to the document and click it
    document.body.appendChild(link);
    link.click();

    // Remove the link element
    document.body.removeChild(link);
  }
</script>

<Button on:click={exportJSON} variant="raised">
  <Icon class="material-icons">system_update_alt</Icon>
  <Label>Export to JSON</Label>
</Button>

<DataTable
  sortable
  bind:sort
  bind:sortDirection
  on:SMUIDataTable:sorted={handleSort}
  table$aria-label="User list"
  style="width: 100%;"
>
  <Head>
    <Row>
      <!--
        Note: whatever you supply to "columnId" is
        appended with "-status-label" and used as an ID
        for the hidden label that describes the sort
        status to screen readers.
 
        You can localize those labels with the
        "sortAscendingAriaLabel" and
        "sortDescendingAriaLabel" props on the DataTable.
      -->
      <Cell columnId="id">
        <!-- For numeric columns, icon comes first. -->
        <IconButton class="material-icons">arrow_upward</IconButton>
        <Label>ID</Label>
      </Cell>
      <Cell columnId="siteName">
        <Label>Site Name</Label>
        <!-- For non-numeric columns, icon comes second. -->
        <IconButton class="material-icons">arrow_upward</IconButton>
      </Cell>
      <Cell columnId="layeredPass">
        <Label>layered Password</Label>
        <IconButton class="material-icons">arrow_upward</IconButton>
      </Cell>
      <Cell sortable={false}>PIN</Cell>
    </Row>
  </Head>
  <Body>
    {#each items as item (item.id)}
      <Row>
        <Cell>{item.id}</Cell>
        <Cell>{item.siteName}</Cell>
        <Cell>{item.layeredPass}</Cell>
        <Cell
          >{item.pin.startBefore1}, {item.pin.numOfChar1}, {item.pin
            .startBefore2}, {item.pin.numOfChar2}</Cell
        >
      </Row>
    {/each}
  </Body>
</DataTable>

<style>
</style>
