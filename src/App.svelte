<script>
  import ValentineProposal from './components/ValentineProposal.svelte'
</script>

<main>
  <ValentineProposal />
</main>

<style global>
  :global(body) {
    margin: 0;
    padding: 0;
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
    background: linear-gradient(135deg, #8b0000 0%, #dc143c 50%, #ff69b4 100%);
    min-height: 100vh;
    overflow: hidden;
  }

  :global(html) {
    width: 100%;
    height: 100%;
  }

  main {
    width: 100%;
    height: 100vh;
    display: flex;
    align-items: center;
    justify-content: center;
  }
</style>
