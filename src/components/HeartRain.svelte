<script>
    import { onMount } from "svelte";
    import gsap from "gsap";

    let hearts = [];

    onMount(() => {
        // Generate initial hearts
        generateHearts();

        // Continuously generate new hearts
        const interval = setInterval(() => {
            generateHearts();
        }, 500);

        return () => clearInterval(interval);
    });

    function generateHearts() {
        // Create 2-4 hearts at random positions
        const count = Math.floor(Math.random() * 3) + 2;
        const newHearts = [];

        for (let i = 0; i < count; i++) {
            const heart = {
                id: `${Date.now()}-${i}`,
                left: Math.random() * 100,
                duration: 3 + Math.random() * 2,
                opacity: 0.3 + Math.random() * 0.5,
                size: 8 + Math.random() * 8, // 8px to 16px
                delay: Math.random() * 0.5,
            };

            newHearts.push(heart);

            // Animate the heart falling
            setTimeout(() => {
                const element = document.querySelector(
                    `[data-heart="${heart.id}"]`,
                );
                if (element) {
                    gsap.to(element, {
                        y: window.innerHeight + 50,
                        opacity: 0,
                        duration: heart.duration,
                        ease: "none",
                        onComplete: () => {
                            hearts = hearts.filter((h) => h.id !== heart.id);
                        },
                    });
                }
            }, heart.delay * 1000);
        }

        hearts = [...hearts, ...newHearts];
    }
</script>

<div class="heart-rain">
    {#each hearts as heart (heart.id)}
        <div
            class="falling-heart"
            data-heart={heart.id}
            style={`
        left: ${heart.left}%;
        font-size: ${heart.size}px;
        opacity: ${heart.opacity};
      `}
        >
            ❤️
        </div>
    {/each}
</div>

<style>
    .heart-rain {
        position: fixed;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        pointer-events: none;
        z-index: 1;
        overflow: hidden;
    }

    .falling-heart {
        position: fixed;
        top: -30px;
        will-change: transform, opacity;
        filter: drop-shadow(0 2px 4px rgba(255, 182, 193, 0.3));
    }
</style>
