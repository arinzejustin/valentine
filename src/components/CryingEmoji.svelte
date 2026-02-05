<script>
    import { onMount } from "svelte";
    import gsap from "gsap";

    let visible = true;

    onMount(() => {
        // Animate in
        const emoji = document.querySelector("[data-emoji]");
        if (emoji) {
            gsap.fromTo(
                emoji,
                { opacity: 0, scale: 0.5, y: -30 },
                {
                    opacity: 1,
                    scale: 1,
                    y: 0,
                    duration: 0.4,
                    ease: "elastic.out(1, 0.5)",
                },
            );

            // Bounce animation
            gsap.to(emoji, {
                y: 10,
                duration: 0.5,
                yoyo: true,
                repeat: 3,
                ease: "sine.inOut",
                delay: 0.4,
            });
        }
    });
</script>

<div class="crying-emoji-overlay" data-emoji>
    <div class="emoji-container">
        <div class="emoji">😢</div>
        <p class="text">Think about it 💔</p>
    </div>
</div>

<style>
    .crying-emoji-overlay {
        position: fixed;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        display: flex;
        align-items: center;
        justify-content: center;
        z-index: 5000;
        pointer-events: none;
    }

    .emoji-container {
        text-align: center;
    }

    .emoji {
        font-size: clamp(60px, 15vw, 120px);
        line-height: 1;
        margin-bottom: 16px;
        filter: drop-shadow(0 4px 8px rgba(0, 0, 0, 0.3));
    }

    .text {
        font-size: clamp(16px, 4vw, 24px);
        color: #fff;
        font-weight: 600;
        text-shadow: 0 3px 10px rgba(0, 0, 0, 0.3);
        margin: 0;
        font-family: "Georgia", serif;
    }

    @media (min-width: 768px) {
        .emoji {
            font-size: 140px;
        }

        .text {
            font-size: 28px;
        }
    }
</style>
