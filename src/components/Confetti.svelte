<script>
    import { onMount } from "svelte";
    import gsap from "gsap";

    let confetti = [];

    onMount(() => {
        // Generate random confetti pieces
        const count = 30;
        for (let i = 0; i < count; i++) {
            confetti.push({
                id: i,
                left: Math.random() * 100,
                delay: Math.random() * 0.2,
                duration: 2 + Math.random() * 1,
                rotation: Math.random() * 360,
                emoji: ["❤️", "💕", "💖", "🎉", "✨"][
                    Math.floor(Math.random() * 5)
                ],
            });
        }
        confetti = confetti;

        // Animate all confetti
        confetti.forEach((piece) => {
            const element = document.querySelector(
                `[data-confetti="${piece.id}"]`,
            );
            if (element) {
                gsap.to(element, {
                    y: window.innerHeight + 50,
                    opacity: 0,
                    rotation: piece.rotation + 360,
                    duration: piece.duration,
                    delay: piece.delay,
                    ease: "power1.in",
                });
            }
        });
    });
</script>

<div class="confetti-container">
    {#each confetti as piece (piece.id)}
        <div
            class="confetti-piece"
            data-confetti={piece.id}
            style={`
        left: ${piece.left}%;
        --delay: ${piece.delay}s;
      `}
        >
            {piece.emoji}
        </div>
    {/each}
</div>

<style>
    .confetti-container {
        position: fixed;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        pointer-events: none;
        z-index: 9998;
        overflow: hidden;
    }

    .confetti-piece {
        position: fixed;
        font-size: 24px;
        top: -20px;
        will-change: transform, opacity;
        animation: fall 3s linear forwards;
        animation-delay: var(--delay);
    }

    @keyframes fall {
        0% {
            opacity: 1;
            transform: translateY(0) rotate(0);
        }
        100% {
            opacity: 0;
            transform: translateY(100vh) rotate(360deg);
        }
    }

    @media (max-width: 768px) {
        .confetti-piece {
            font-size: 20px;
        }
    }
</style>
