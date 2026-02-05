<script>
  import { onMount } from "svelte";
  import gsap from "gsap";
  import Button from "./Button.svelte";
  import CryingEmoji from "./CryingEmoji.svelte";
  import Confetti from "./Confetti.svelte";
  import HeartRain from "./HeartRain.svelte";

  let yesButtonCount = 0;
  let noButtonCount = 0;
  let showCrying = false;
  let showCelebration = false;
  let yesButtons = []; // Array to track all YES buttons
  let audioPlaying = false;
  let buttonsVisible = true;
  let isAnimating = false; // Lock to prevent rapid-click issues
  let currentAnimation = null; // Track current GSAP animation
  let cryingTimeout = null; // Track crying emoji timeout to prevent fast-click issues
  let preloadedAudio = null; // Preloaded audio element
  let assetsPreloaded = false; // Flag to track if assets are ready

  // Initial button positions - will be calculated on mount
  let mainYesButtonPos = { x: 0, y: 0 };
  let noButtonPos = { x: 0, y: 0 };

  let containerWidth = typeof window !== "undefined" ? window.innerWidth : 360;
  let containerHeight =
    typeof window !== "undefined" ? window.innerHeight : 800;

  onMount(() => {
    // Get actual window dimensions
    containerWidth = window.innerWidth;
    containerHeight = window.innerHeight;

    // Calculate button positions to be centered below the message
    // Message container is at top 8% with some content height
    const buttonAreaY = Math.min(containerHeight * 0.55, containerHeight - 200);
    const centerX = containerWidth / 2;
    const buttonGap = 30; // Gap between buttons
    const buttonWidth = 120; // Approximate button width

    // Position YES and NAH buttons side by side, centered
    mainYesButtonPos = {
      x: centerX - buttonWidth - buttonGap / 2,
      y: buttonAreaY,
    };
    noButtonPos = {
      x: centerX + buttonGap / 2,
      y: buttonAreaY,
    };

    // Animate main question from top to bottom
    const mainQuestion = document.querySelector(".main-question");
    if (mainQuestion) {
      gsap.fromTo(
        mainQuestion,
        { opacity: 0, y: -50 },
        { opacity: 1, y: 0, duration: 1, ease: "power2.out" },
      );
    }

    // Animate subtext
    const subtext = document.querySelector(".subtext");
    if (subtext) {
      gsap.fromTo(
        subtext,
        { opacity: 0, y: -30 },
        {
          opacity: 1,
          y: 0,
          duration: 1,
          delay: 0.3,
          ease: "power2.out",
        },
      );
    }

    // Animate romantic message
    const romanticMsg = document.querySelector(".romantic-message");
    if (romanticMsg) {
      gsap.fromTo(
        romanticMsg,
        { opacity: 0, y: -30 },
        {
          opacity: 1,
          y: 0,
          duration: 1,
          delay: 0.6,
          ease: "power2.out",
        },
      );
    }

    // Listen for window resize
    const handleResize = () => {
      containerWidth = window.innerWidth;
      containerHeight = window.innerHeight;
    };

    window.addEventListener("resize", handleResize);

    // Preload assets (GIFs and audio) for instant display
    preloadAssets();

    return () => {
      window.removeEventListener("resize", handleResize);
      // Clean up preloaded audio
      if (preloadedAudio) {
        preloadedAudio.pause();
        preloadedAudio = null;
      }
      // Clear any pending timeout
      if (cryingTimeout) {
        clearTimeout(cryingTimeout);
      }
    };
  });

  /**
   * Calculates a random position for the NO button
   * Ensures it stays within viewport with padding
   */
  function getRandomNoButtonPos() {
    const padding = 20;
    const buttonWidth = 140; // NAH button width with padding (16px 32px = 64px + text)
    const buttonHeight = 70; // NO button height with padding (16px + text)

    // Use actual viewport dimensions for safety
    const viewportWidth = window.innerWidth || 360;
    const viewportHeight = window.innerHeight || 800;

    // Calculate safe bounds - leave room for button size
    const minX = padding;
    const maxX = Math.max(minX + 10, viewportWidth - buttonWidth - padding);
    const minY = 110; // Below the YES/NAH buttons at top
    const maxY = Math.max(
      minY + 10,
      viewportHeight - buttonHeight - padding - 80,
    ); // Leave space at bottom

    // Generate random position within safe bounds
    const randomX = Math.random() * (maxX - minX) + minX;
    const randomY = Math.random() * (maxY - minY) + minY;

    return {
      x: Math.max(minX, Math.min(randomX, maxX)),
      y: Math.max(minY, Math.min(randomY, maxY)),
    };
  }

  function resetNoButtonPos() {
    noButtonPos = {
      x: containerWidth / 2 + 80,
      y: containerHeight - 140,
    };
  }

  /**
   * Handle NO button click
   * 1. Store current NO position
   * 2. Create new YES button at that position
   * 3. Move NO button to random position
   * 4. Add exclamation mark to all YES buttons
   * 5. Show crying emoji
   */
  function handleNoClick() {
    // Kill any ongoing animation to prevent conflicts on rapid clicks
    if (currentAnimation) {
      currentAnimation.kill();
      currentAnimation = null;
    }

    // Store the current NO button position before moving
    const prevNoPos = { ...noButtonPos };

    // Create new YES button at a random position
    const randomYesPos = getRandomNoButtonPos();
    yesButtons = [
      ...yesButtons,
      {
        id: `yes-${yesButtonCount}`,
        x: randomYesPos.x,
        y: randomYesPos.y,
      },
    ];

    // Calculate new position for NO button
    const newPos = getRandomNoButtonPos();

    // Increment YES button count (affects exclamation marks)
    yesButtonCount++;
    noButtonCount++;

    // Show crying emoji - clear any existing timeout first to prevent fast-click bugs
    if (cryingTimeout) {
      clearTimeout(cryingTimeout);
    }
    showCrying = true;
    cryingTimeout = setTimeout(() => {
      showCrying = false;
      cryingTimeout = null;
    }, 2500); // Slightly longer duration for visibility

    // Set final position immediately (no animation conflicts)
    // Then animate with GSAP for smooth visual
    const animationTarget = { progress: 0 };
    currentAnimation = gsap.to(animationTarget, {
      progress: 1,
      duration: 0.3,
      ease: "power2.out",
      onUpdate: function () {
        const progress = animationTarget.progress;
        noButtonPos = {
          x: prevNoPos.x + (newPos.x - prevNoPos.x) * progress,
          y: prevNoPos.y + (newPos.y - prevNoPos.y) * progress,
        };
      },
      onComplete: function () {
        // Ensure final position is set correctly
        noButtonPos = { ...newPos };
        currentAnimation = null;
      },
    });
  }

  /**
   * Handle YES button click
   * 1. Play romantic music
   * 2. Show celebration animation
   * 3. Trigger confetti
   */
  function handleYesClick() {
    showCelebration = true;

    // Play music
    playRomanticMusic();

    // Trigger celebration animation
    const celebrationEl = document.querySelector("[data-celebration]");
    if (celebrationEl) {
      gsap.fromTo(
        celebrationEl,
        { opacity: 0, scale: 0.8 },
        {
          opacity: 1,
          scale: 1,
          duration: 0.6,
          ease: "elastic.out(1, 0.5)",
        },
      );
    }
  }

  /**
   * Preload assets (GIFs and audio) for instant display
   */
  function preloadAssets() {
    // Preload celebration GIFs
    const gifUrls = [
      "https://media.giphy.com/media/l0MYt5jPR6QX5pnqM/giphy.gif",
      "https://media.giphy.com/media/26tOZ42Mg6pbTUPHW/giphy.gif",
    ];

    gifUrls.forEach((url) => {
      const img = new Image();
      img.src = url;
    });

    // Preload audio
    preloadedAudio = new Audio();
    preloadedAudio.src = "/mp3/romantic.mp3";
    preloadedAudio.volume = 0.5;
    preloadedAudio.loop = true;
    preloadedAudio.preload = "auto";

    // Start loading the audio
    preloadedAudio.load();

    assetsPreloaded = true;
  }

  /**
   * Play romantic background music
   * Uses preloaded audio for instant playback
   */
  function playRomanticMusic() {
    if (audioPlaying) return;

    audioPlaying = true;

    // Use preloaded audio if available
    if (preloadedAudio) {
      preloadedAudio.play().catch((/** @type {any} */ _err) => {
        audioPlaying = false;
      });
    } else {
      // Fallback: create new audio element
      const audio = new Audio();
      audio.src = "/mp3/romantic.mp3";
      audio.volume = 0.5;
      audio.loop = true;
      audio.play().catch((/** @type {any} */ _err) => {
        audioPlaying = false;
      });
    }
  }

  function getYesButtonText() {
    if (
      yesButtonCount === 5 ||
      yesButtonCount === 10 ||
      yesButtonCount === 15 ||
      yesButtonCount === 20 ||
      yesButtonCount === 25
    )
      return "Please be my val USOM ❤️";
    return "YES" + "!".repeat(yesButtonCount);
  }
</script>

<div class="valentine-container">
  <!-- Heart rain background -->
  <HeartRain />

  <!-- Main romantic message -->
  <div class="message-container">
    <div class="name-tag">✨ To USOM ✨</div>
    <h1 class="main-question">Will you be my Valentine?</h1>
    <p class="subtext">I promise to make you smile every single day 💕</p>
    <p class="romantic-message">
      Every moment with you feels like a beautiful dream. Your smile brightens
      my darkest days, and your presence makes my heart complete. I want to
      spend forever creating memories with you, holding your hand through life's
      adventures, and loving you more deeply with each passing day.
    </p>
  </div>

  <!-- YES buttons that were created from previous NO clicks -->
  {#each yesButtons as yesBtn (yesBtn.id)}
    <Button
      type="yes"
      text={getYesButtonText()}
      x={yesBtn.x}
      y={yesBtn.y}
      onClick={handleYesClick}
    />
  {/each}

  <!-- Main YES button (always visible, positioned next to NAH button) -->
  <Button
    type="yes"
    text="YES ❤️"
    x={mainYesButtonPos.x}
    y={mainYesButtonPos.y}
    onClick={handleYesClick}
  />

  <!-- NO button (moves when clicked) -->
  <Button
    type="no"
    text="NAH 💔"
    x={noButtonPos.x}
    y={noButtonPos.y}
    onClick={handleNoClick}
  />

  <!-- Crying emoji overlay (shows when NO clicked) -->
  {#if showCrying}
    <CryingEmoji />
  {/if}

  <!-- Celebration state -->
  {#if showCelebration}
    <div class="celebration-overlay" data-celebration>
      <div class="celebration-content">
        <img
          src="https://media.giphy.com/media/l0MYt5jPR6QX5pnqM/giphy.gif"
          alt="celebration hearts"
          class="celebration-gif"
        />
        <img
          src="https://media.giphy.com/media/26tOZ42Mg6pbTUPHW/giphy.gif"
          alt="love celebration"
          class="celebration-gif-secondary"
        />
        <h2 class="celebration-message">
          Yes! I'm the happiest man alive! 🎉💕
        </h2>
        <p class="celebration-subtext">
          You made me the luckiest person alive!
        </p>
        <Confetti />
      </div>
    </div>
  {/if}
</div>

<style>
  .valentine-container {
    position: relative;
    width: 100vw;
    height: 100vh;
    display: flex;
    align-items: center;
    justify-content: center;
    overflow: visible;
    background: linear-gradient(135deg, #8b0000 0%, #dc143c 50%, #ff69b4 100%);
  }

  .message-container {
    position: absolute;
    top: 8%;
    left: 50%;
    transform: translateX(-50%);
    text-align: center;
    z-index: 10;
    pointer-events: none;
    padding: 0 20px;
    max-width: 90vw;
    min-width: 280px;
  }

  .name-tag {
    font-size: clamp(20px, 5vw, 32px);
    font-family: "Great Vibes", cursive;
    background: linear-gradient(135deg, #fff 0%, #ffe4e1 100%);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    background-clip: text;
    color: #fff;
    text-shadow: 0 2px 10px rgba(0, 0, 0, 0.2);
    margin-bottom: 16px;
    font-weight: 400;
    letter-spacing: 1px;
  }

  .main-question {
    font-size: clamp(36px, 8vw, 56px);
    font-family: "Great Vibes", cursive;
    background: linear-gradient(135deg, #fff 0%, #ffe4e1 50%, #ffb6c1 100%);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    background-clip: text;
    color: #fff;
    font-weight: 700;
    letter-spacing: 1px;
    text-shadow: 0 4px 12px rgba(0, 0, 0, 0.2);
    margin-bottom: 16px;
    animation: slideDown 1s ease-out;
  }

  .subtext {
    font-size: clamp(14px, 3vw, 18px);
    color: rgba(255, 255, 255, 0.95);
    font-weight: 400;
    font-family: "Lora", serif;
    text-shadow: 0 2px 8px rgba(0, 0, 0, 0.2);
    animation: fadeIn 1s ease-out 0.3s both;
    margin-bottom: 20px;
  }

  .romantic-message {
    font-size: clamp(13px, 2.5vw, 16px);
    color: rgba(255, 255, 255, 0.9);
    font-weight: 400;
    font-family: "Lora", serif;
    line-height: 1.6;
    text-shadow: 0 2px 8px rgba(0, 0, 0, 0.2);
    animation: fadeIn 1s ease-out 0.6s both;
    max-width: 500px;
    margin: 0 auto;
    font-style: italic;
    letter-spacing: 0.3px;
  }

  .celebration-overlay {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: radial-gradient(
      circle,
      rgba(255, 192, 203, 0.3),
      rgba(139, 0, 0, 0.2)
    );
    display: flex;
    align-items: center;
    justify-content: center;
    z-index: 9999;
    pointer-events: none;
    backdrop-filter: blur(4px);
  }

  .celebration-content {
    text-align: center;
    animation: celebrationPop 0.6s cubic-bezier(0.34, 1.56, 0.64, 1);
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 20px;
  }

  .celebration-gif {
    width: clamp(200px, 60vw, 400px);
    height: auto;
    border-radius: 20px;
    box-shadow: 0 8px 32px rgba(0, 0, 0, 0.3);
    animation: slideInScale 0.8s cubic-bezier(0.34, 1.56, 0.64, 1);
  }

  .celebration-gif-secondary {
    width: clamp(150px, 45vw, 300px);
    height: auto;
    border-radius: 16px;
    box-shadow: 0 6px 24px rgba(0, 0, 0, 0.25);
    animation: slideInScale 0.8s cubic-bezier(0.34, 1.56, 0.64, 1) 0.3s both;
  }

  .celebration-message {
    font-size: clamp(20px, 5vw, 40px);
    color: #fff;
    font-weight: 700;
    text-shadow: 0 4px 16px rgba(0, 0, 0, 0.3);
    margin-bottom: 12px;
    font-family: "Georgia", serif;
  }

  .celebration-subtext {
    font-size: clamp(14px, 3vw, 18px);
    color: rgba(255, 255, 255, 0.9);
    text-shadow: 0 2px 8px rgba(0, 0, 0, 0.2);
  }

  @keyframes slideDown {
    from {
      opacity: 0;
      transform: translateY(-50px);
    }
    to {
      opacity: 1;
      transform: translateY(0);
    }
  }

  @keyframes slideInScale {
    from {
      opacity: 0;
      transform: scale(0.7);
    }
    to {
      opacity: 1;
      transform: scale(1);
    }
  }

  @keyframes fadeInScale {
    from {
      opacity: 0;
      transform: translateX(-50%) scale(0.9);
    }
    to {
      opacity: 1;
      transform: translateX(-50%) scale(1);
    }
  }

  @keyframes fadeIn {
    from {
      opacity: 0;
    }
    to {
      opacity: 1;
    }
  }

  @keyframes celebrationPop {
    0% {
      transform: scale(0.5);
      opacity: 0;
    }
    50% {
      transform: scale(1.1);
    }
    100% {
      transform: scale(1);
      opacity: 1;
    }
  }

  /* Mobile-first media queries */
  @media (min-width: 768px) {
    .main-question {
      font-size: 52px;
    }

    .subtext {
      font-size: 20px;
    }

    .message-container {
      top: 20%;
    }
  }

  /* Floating hearts background animation */
  @keyframes float {
    0%,
    100% {
      transform: translateY(0) translateX(0);
    }
    25% {
      transform: translateY(-20px) translateX(10px);
    }
    50% {
      transform: translateY(-40px) translateX(-10px);
    }
    75% {
      transform: translateY(-20px) translateX(10px);
    }
  }
</style>
