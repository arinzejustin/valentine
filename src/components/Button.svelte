<script>
  /**
   * Button Component - shadcn-inspired design
   *
   * Props:
   * - type: 'yes' or 'no' - determines styling (primary vs secondary/destructive)
   * - text: button label
   * - x, y: absolute position on screen
   * - onClick: callback function when clicked
   */

  export let type = "yes";
  export let text = "YES";
  export let x = 0;
  export let y = 0;
  export let onClick = () => {};

  function handleClick(e) {
    e.preventDefault();
    e.stopPropagation();
    onClick();
  }
</script>

<button
  class={`btn btn-${type}`}
  style={`left: ${x}px; top: ${y}px;`}
  data-button={type === "no" ? "no" : undefined}
  on:click={handleClick}
>
  {text}
</button>

<style>
  .btn {
    position: fixed;
    display: inline-flex;
    align-items: center;
    justify-content: center;
    gap: 8px;
    white-space: nowrap;

    /* shadcn-style sizing */
    height: 44px;
    padding: 0 26px;

    /* Typography */
    font-size: 14px;
    font-weight: 500;
    font-family:
      system-ui,
      -apple-system,
      BlinkMacSystemFont,
      "Segoe UI",
      Roboto,
      sans-serif;
    letter-spacing: -0.01em;

    /* Shape */
    border-radius: 999px;
    border: none;

    /* Interaction */
    cursor: pointer;
    touch-action: manipulation;
    -webkit-tap-highlight-color: transparent;
    user-select: none;

    /* Smooth transitions */
    transition: all 0.15s ease;

    /* Layering */
    z-index: 100;
  }

  .btn:focus-visible {
    outline: 2px solid hsl(0 0% 100% / 0.5);
    outline-offset: 2px;
  }

  .btn:disabled {
    pointer-events: none;
    opacity: 0.5;
  }

  /* YES button - Primary style (solid) */
  .btn-yes {
    background: hsl(0 72% 51%);
    color: hsl(0 0% 100%);
    box-shadow:
      0 1px 3px 0 rgb(0 0 0 / 0.1),
      0 1px 2px -1px rgb(0 0 0 / 0.1),
      inset 0 1px 0 0 hsl(0 0% 100% / 0.1);
    z-index: 200;
  }

  .btn-yes:hover {
    background: hsl(0 72% 45%);
    box-shadow:
      0 4px 6px -1px rgb(0 0 0 / 0.1),
      0 2px 4px -2px rgb(0 0 0 / 0.1),
      inset 0 1px 0 0 hsl(0 0% 100% / 0.15);
    transform: translateY(-1px);
  }

  .btn-yes:active {
    background: hsl(0 72% 40%);
    transform: translateY(0);
    box-shadow:
      0 1px 2px 0 rgb(0 0 0 / 0.05),
      inset 0 1px 0 0 hsl(0 0% 0% / 0.05);
  }

  /* NO button - Secondary/Ghost style */
  .btn-no {
    background: hsl(0 0% 100% / 0.1);
    color: hsl(0 0% 100%);
    border: 1px solid hsl(0 0% 100% / 0.2);
    backdrop-filter: blur(8px);
    -webkit-backdrop-filter: blur(8px);
    box-shadow:
      0 1px 3px 0 rgb(0 0 0 / 0.1),
      0 1px 2px -1px rgb(0 0 0 / 0.1);
  }

  .btn-no:hover {
    background: hsl(0 0% 100% / 0.2);
    border-color: hsl(0 0% 100% / 0.3);
    transform: translateY(-1px);
    box-shadow:
      0 4px 6px -1px rgb(0 0 0 / 0.1),
      0 2px 4px -2px rgb(0 0 0 / 0.1);
  }

  .btn-no:active {
    background: hsl(0 0% 100% / 0.15);
    transform: translateY(0);
    box-shadow: 0 1px 2px 0 rgb(0 0 0 / 0.05);
  }

  /* Mobile-optimized touch targets */
  @media (max-width: 768px) {
    .btn {
      height: 48px;
      padding: 0 20px;
      font-size: 15px;
    }
  }

  /* Smaller screens */
  @media (max-width: 430px) {
    .btn {
      height: 44px;
      padding: 0 16px;
      font-size: 14px;
    }
  }
</style>
