<!-- <script>
    import { onMount } from "svelte";
    import {writeable} from "svelte/store"

  export let icon_data = [];
  export const size = writeable({width: 0, height: 0})

  let container;

  onMount(() => {
    const { width, height } = container.getBoundingClientRect()
    size.set({width, height})
  })
</script>

{#each icon_data as icon}
    <img src=>
{/each} -->
