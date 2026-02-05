# 💕 Valentine Proposal Website

A beautiful, romantic Valentine proposal website built with **Svelte** featuring interactive buttons, animations, and a celebration experience.

## 🎯 Features

✨ **Mobile-First Design** - Optimized for smartphones and tablets
❤️ **Romantic Animations** - Smooth GSAP animations and transitions
🎉 **Interactive Logic** - NO button escapes, YES button multiplies
😢 **Emotional Moments** - Crying emoji overlay for added playfulness
🎵 **Background Music** - Romantic music plays on YES click
🎊 **Confetti Celebration** - Animated confetti and celebration message
🎨 **Beautiful Theme** - Gradient red background with romantic styling

## 🧰 Tech Stack

- **Framework:** Svelte
- **Build Tool:** Vite
- **Animation:** GSAP 3.12+
- **Audio:** HTML5 Audio API
- **Styling:** CSS3

## 📦 Installation

### Prerequisites
- Node.js 16+ and npm

### Setup

```bash
# Install dependencies
npm install

# Start development server (opens at http://localhost:5173)
npm run dev

# Build for production
npm run build

# Preview production build
npm run preview
```

## 🎮 How It Works

### NO Button Behavior
When the user clicks "NO":
1. The NO button teleports to a random position on screen
2. A new YES button appears where the NO button was
3. All YES buttons gain an exclamation mark (YES → YES! → YES!! etc.)
4. A crying emoji overlay appears briefly with "Think about it 💔"

### YES Button Behavior
When the user clicks any YES button:
1. Romantic background music plays
2. The screen shows a celebration message
3. Confetti falls from the top
4. A warm glow overlay covers the screen

### Random NO Button Positioning
- Calculates random position within viewport bounds
- Respects safe padding margins
- Never goes off-screen
- Uses smooth elastic animation

## 🎵 Music Setup

The website currently uses a placeholder royalty-free music URL. To use your own music:

1. **Option 1:** Replace the URL in `ValentineProposal.svelte`
   ```javascript
   audio.src = 'https://your-music-url.mp3'
   ```

2. **Option 2:** Place an `audio/romantic.mp3` file in the `public` folder
   ```javascript
   audio.src = '/audio/romantic.mp3'
   ```

3. **Recommended Free Resources:**
   - Pixabay: https://pixabay.com/music/
   - Freepik: https://www.freepik.com/
   - YouTube Audio Library: https://www.youtube.com/audiolibrary

## 🎨 Customization

### Change Colors
Edit the gradient colors in `ValentineProposal.svelte`:
```css
background: linear-gradient(135deg, #8b0000 0%, #dc143c 50%, #ff69b4 100%);
```

### Customize Messages
Edit text in `ValentineProposal.svelte`:
- Main question: `.main-question`
- Subtext: `.subtext`
- Celebration message: `.celebration-message`

### Adjust Animation Speed
Modify GSAP timing values in components:
```javascript
gsap.to(element, {
  duration: 0.4,  // Change this value
  ease: 'elastic.out(1, 0.3)'
})
```

## 📱 Mobile Optimization

- Touch-friendly button sizes (min 48x48px)
- Responsive font sizes using `clamp()`
- No hover-only interactions
- Optimized animations for low-end devices
- Full viewport width/height utilization

## 🚀 Deployment

### Vercel (Recommended)
```bash
npm i -g vercel
vercel
```

### Netlify
```bash
npm run build
# Drag-and-drop `dist` folder to Netlify
```

### GitHub Pages
Add to `vite.config.js`:
```javascript
base: '/valentine-proposal/'
```

## 📂 Project Structure

```
src/
├── main.js                          # Entry point
├── App.svelte                       # Root component
└── components/
    ├── ValentineProposal.svelte     # Main logic & state
    ├── Button.svelte                # YES/NO button component
    ├── CryingEmoji.svelte           # Crying emoji overlay
    └── Confetti.svelte              # Confetti animation
```

## 🔧 Key Implementation Details

### NO Button Randomization
```javascript
function getRandomNoButtonPos() {
  const padding = 20
  const buttonWidth = 80
  const buttonHeight = 50
  
  const maxX = Math.max(padding, containerWidth - buttonWidth - padding)
  const maxY = Math.max(padding, containerHeight - buttonHeight - padding)
  
  const randomX = Math.random() * (maxX - padding) + padding
  const randomY = Math.random() * (maxY - padding) + padding
  
  return { x: Math.min(randomX, maxX), y: Math.min(randomY, maxY) }
}
```

### YES Button Mutation
Every NO click adds an exclamation mark:
```javascript
function getYesButtonText() {
  return 'YES' + '!'.repeat(yesButtonCount)
}
```

### Z-Index Hierarchy
- Crying emoji: 5000 (highest)
- Confetti: 9998
- Celebration overlay: 9999
- YES buttons: 200
- NO button: 100
- Content: 10

## 🎯 Browser Support

- Chrome/Edge 90+
- Firefox 88+
- Safari 14+
- Mobile browsers (iOS Safari, Chrome Mobile)

## 📜 License

Free to use for personal Valentine proposals! 💕

## 💬 Personalization Tips

1. **Add Custom Names:** Modify the messages to include specific names
2. **Custom Colors:** Adjust the gradient to match her favorite colors
3. **Personal Music:** Add your favorite romantic song
4. **Message Content:** Write personalized celebration messages
5. **Timeline:** Deploy early morning or schedule for special time

## 🎁 Tips for Maximum Impact

- Deploy and test on mobile before showing
- Ensure sound works (check browser autoplay policies)
- Consider deploying on a domain you own
- Add a QR code pointing to the site
- Test on low-end phones for smooth performance

---

Made with ❤️ for your special moment. Good luck! 💕
