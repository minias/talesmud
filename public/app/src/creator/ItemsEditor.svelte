<style>

</style>

<script>
  import Sprites from "./../game/Sprites.svelte";
  import { onMount } from "svelte";
  import CRUDEditor from "./CRUDEditor.svelte";
  import { createStore } from "./CRUDEditorStore.js";
  import { v4 as uuidv4 } from "uuid";

  import {
    getItem,
    getItems,
    createItem,
    updateItem,
    deleteItem,
  } from "../api/items.js";

  import {
    getItemQualities,
    getItemTypes,
    getItemSubTypes,
    getItemSlots,
  } from "../api/item-templates.js";

  const config = {
    title: "Manage Items",
    actions: [],
    get: getItems,
    getElement: getItem,
    create: createItem,
    update: updateItem,
    delete: deleteItem,
    refreshUI: () => {
      let elems = document.querySelectorAll("select");
      let instances = M.FormSelect.init(elems, {});

      // second time to fix the selects
      setTimeout(function () {
        let elems = document.querySelectorAll("select");
        let instances = M.FormSelect.init(elems, {});
      }, 50);
    },

    new: (select) => {
      select({
        id: uuidv4(),
        name: "Unnamed Item",
        description: "",
        detail: "",
        type: "",
        slot: "inventory",
        quality: "normal",
        level: 1,
        properties: new Map(),
        attributes: new Map(),
        noPickup: false,
        tags: [],
        isNew: true,
      });
    },

    badge: (element) => {
      return element.quality + " " + element.subType;
    },
  };
  // create store outside of the component to use it in the slot..
  const store = createStore();

  ///////// ADDITIONAL DATA
  // additional data
  let itemQualities = [];
  let itemTypes = [];
  let itemSubTypes = [];
  let itemSlots = [];
  // create level array
  let levels = [];
  for (let i = 1; i <= 50; i++) {
    levels.push(i);
  }

  onMount(async () => {
    getItemQualities((q) => (itemQualities = q));
    getItemTypes((t) => (itemTypes = t));
    getItemSubTypes((st) => (itemSubTypes = st));
    getItemSlots((s) => (itemSlots = s));
  });
  /////////
</script>

<CRUDEditor store="{store}" config="{config}">

  <span slot="hero" class="col s1 valign-wrapper">
    <Sprites item="weapon" />
  </span>

  <div slot="content">
    <div class="row">

      <div class="margininput input-field col s1">
        <select bind:value="{$store.selectedElement.level}" on:change>
          {#each levels as lvl}
            <option value="{lvl}">{lvl}</option>
          {/each}
        </select>
        <label>Level</label>
      </div>

      <div class="margininput input-field col s5">
        <select bind:value="{$store.selectedElement.type}" on:change>
          <option value="" disabled selected>Item Type</option>
          {#each itemTypes as type}
            <option value="{type}">{type.capitalize()}</option>
          {/each}
        </select>
        <label>Select Item Type</label>
      </div>

      <div class="margininput input-field col s5">
        <select bind:value="{$store.selectedElement.subType}" on:change>
          <option value="" selected>Item Subtype</option>
          {#each itemSubTypes as subType}
            <option value="{subType}">{subType.capitalize()}</option>
          {/each}
        </select>
        <label>Select Item Sub Type</label>
      </div>

    </div>
    <div class="row">

      <div class="margininput input-field col s5">
        <select bind:value="{$store.selectedElement.quality}" on:change>
          <option value="" disabled selected>Item Quality</option>
          {#each itemQualities as quality}
            <option value="{quality}">{quality.capitalize()}</option>
          {/each}

        </select>
        <label>Select Item Quality</label>
      </div>

      <div class="margininput input-field col s5">
        <select
          class="margininput"
          bind:value="{$store.selectedElement.slot}"
          on:change
        >
          <option value="" selected>Item Slot</option>
          {#each itemSlots as slot}
            <option class="select" value="{slot}">{slot.capitalize()}</option>
          {/each}
        </select>
        <label>Select Item Slot</label>
      </div>

    </div>
  </div>
</CRUDEditor>
