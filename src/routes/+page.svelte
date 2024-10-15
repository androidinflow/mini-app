<script>
  import { onMount } from "svelte";

  let user = null;
  let isReady = false;
  let colorScheme = "light"; // Add this line to define colorScheme

  function showAlert() {
    if (window.Telegram?.WebApp) {
      window.Telegram.WebApp.showAlert("Hello from MainButton!");
    }
  }

  onMount(() => {
    if (window.Telegram?.WebApp) {
      const tg = window.Telegram.WebApp;
      tg.ready();
      user = tg.initDataUnsafe.user;
      isReady = true;

      // Set colorScheme based on Telegram theme
      colorScheme = tg.colorScheme;

      const mainButton = tg.MainButton;
      mainButton.setText("Click Me!");
      mainButton.show();
      mainButton.onClick(showAlert);
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
