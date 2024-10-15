<script>
  import { onMount } from "svelte";

  let user = null;
  let isReady = false;

  onMount(() => {
    if (window.Telegram && window.Telegram.WebApp) {
      const tg = window.Telegram.WebApp;
      tg.ready();
      user = tg.initDataUnsafe.user;
      isReady = true;
    }
  });
</script>

<h1>Welcome to Telegram Mini App</h1>

{#if isReady}
  {#if user}
    <p>Hello, {user.username || "User"}!</p>
  {:else}
    <p>User data not available.</p>
  {/if}
{:else}
  <p>Loading...</p>
{/if}

<p>This is a Telegram Mini App built with SvelteKit.</p>
