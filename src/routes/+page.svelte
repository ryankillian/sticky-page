<script lang="ts">
  import "iconify-icon";
  import { copy } from "svelte-copy";
  import toast from "svelte-french-toast";
  import type { Snapshot } from "./$types";
  import { onDestroy, onMount } from "svelte";
  import { browser } from "$app/environment";

  let comment = "";
  let textAreaElement: HTMLTextAreaElement;

  export const snapshot: Snapshot = {
    capture: () => comment,
    restore: (value) => (comment = value),
  };

  function beforeUnloadHandler(event: BeforeUnloadEvent) {
    localStorage.setItem("sveltekit:snapshot", comment);
    return "";
  }
  onMount(() => {
    let snapshot = localStorage.getItem("sveltekit:snapshot");
    if (snapshot) {
      comment = snapshot;
    }
    window.addEventListener("beforeunload", beforeUnloadHandler);
  });

  onDestroy(() => {
    if (browser) {
      window.removeEventListener("beforeunload", beforeUnloadHandler);
    }
  });

  function copyToClipboard() {
    toast("Copied!", {
      style: "background: #222; color: grey;",
    });
    textAreaElement.focus();
  }
  function eraseText() {
    toast("Erased!", {
      style: "background: #222; color: grey;",
    });
    comment = "";
    textAreaElement.focus();
  }
</script>

<header>
  <!-- svelte-ignore a11y-click-events-have-key-events -->
  <iconify-icon
    icon="material-symbols:content-copy-outline-rounded"
    use:copy={comment}
    on:click={copyToClipboard}
  />
  <!-- svelte-ignore a11y-click-events-have-key-events -->
  <iconify-icon
    icon="material-symbols:delete-forever-outline-rounded"
    on:click={eraseText}
  />
</header>
<!-- svelte-ignore a11y-autofocus -->
<main>
  <textarea
    bind:value={comment}
    bind:this={textAreaElement}
    placeholder="Start typing here..."
    autofocus
    autocorrect="false"
    spellcheck="false"
  />
</main>
