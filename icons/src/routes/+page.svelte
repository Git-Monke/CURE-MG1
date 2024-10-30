<script>
  import { writable } from "svelte/store";
  import icon_names from "./icon_names";
  import { onMount } from "svelte";

  let size;
  let object;
  let icons;

  class Icon {
    constructor(x, y, name) {
      Object.assign(this, { x, y, name });
    }
  }

  function randUpTo(n) {
    return Math.floor(Math.random() * n);
  }

  function overlapsExisting(x, y, icons) {
    for (let icon of icons) {
      if (
        (x <= icon.x + 16 && x >= icon.x) ||
        (y <= icon.y + 16 && y >= icon.y)
      ) {
        return true;
      }
    }
  }

  function randIcons(n) {
    let icon_list = [];

    for (let i = 0; i < n; i++) {
      let rand_icon_name = icon_names[randUpTo(icon_names.length)];

      let x;
      let y;

      do {
        x = randUpTo(size.width - 64);
        y = randUpTo(size.height - 64);
      } while (overlapsExisting(x, y, icon_list));

      icon_list.push(new Icon(x, y, rand_icon_name));
    }

    icons = icon_list;
  }

  onMount(() => {
    let { width, height } = object.getBoundingClientRect();
    size = { width, height };

    randIcons(5);
  });
</script>

<div class="flex flex-col h-screen">
  <h1 class="border-b-4 h-[5vh] flex items-center justify-center">
    Memory Game
  </h1>

  <div class="w-full h-full p-10">
    <div
      bind:this={object}
      class="bg-white w-full h-full border-4 rounded-lg relative"
    >
      {#each icons as icon}
        <img
          src={`/icons/${icon.name}.svg`}
          style={`position: absolute; top: ${icon.y}px; left: ${icon.x}px;`}
          class="w-16 h-16"
          alt="Game requires images"
        />
      {/each}
    </div>
  </div>
</div>
