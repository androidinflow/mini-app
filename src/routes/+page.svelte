<script>
  import { onMount } from "svelte";

  let user = null;
  let isReady = false;

  function showAlert() {
    if (window.Telegram && window.Telegram.WebApp) {
      window.Telegram.WebApp.showAlert("Hello from MainButton!");
    }
  }

  onMount(() => {
    if (window.Telegram && window.Telegram.WebApp) {
      const tg = window.Telegram.WebApp;
      tg.ready();
      user = tg.initDataUnsafe.user;
      isReady = true;

      if (window.Telegram && window.Telegram.WebApp) {
        const mainButton = window.Telegram.WebApp.MainButton;
        mainButton.setText("Click Me!");
        mainButton.show();
        mainButton.onClick(showAlert);
      }
    }
  });
</script>

<div class={colorScheme}>
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
</div>

<style>
  .light {
    background-color: var(--tg-theme-bg-color, #ffffff);
    color: var(--tg-theme-text-color, #000000);
  }
  .dark {
    background-color: var(--tg-theme-bg-color, #2f2f2f);
    color: var(--tg-theme-text-color, #ffffff);
  }
</style>
