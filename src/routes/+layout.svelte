<main>
  <slot />
</main>

<style>
  :global(body) {
    font-family: Arial, sans-serif;
    margin: 0;
    padding: 20px;
    background-color: var(--tg-theme-bg-color, #ffffff);
    color: var(--tg-theme-text-color, #000000);
  }

  main {
    max-width: 800px;
    margin: 0 auto;
  }
</style>
