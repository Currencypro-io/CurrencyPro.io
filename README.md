# 🍳 MealMate AI Food Scanner

AI-powered food recognition for MealMate using Claude Vision API. Point your camera at food items and automatically add them to your pantry.

## 📁 File Structure

```
mealmate-scanner/
├── app/
│   ├── api/
│   │   └── scan-food/
│   │       └── route.js      # API endpoint for Claude Vision
│   └── scan/
│       └── page.jsx          # Scanner page example
├── components/
│   └── FoodScanner.jsx       # Main scanner component
├── hooks/
│   └── useFoodScanner.js     # Camera & scanning hook
├── lib/
│   └── pantry.js             # Supabase pantry service
└── supabase/
    └── migrations/
        └── 001_pantry_items.sql  # Database schema
```

## 🚀 Quick Setup

### 1. Install Dependencies

```bash
npm install @anthropic-ai/sdk @supabase/supabase-js
```

### 2. Set Environment Variables

Add to your `.env.local`:

```env
# Supabase (already configured)
NEXT_PUBLIC_SUPABASE_URL=your_supabase_url
NEXT_PUBLIC_SUPABASE_ANON_KEY=your_supabase_anon_key

# Claude API (optional - users can add their own key)
CLAUDE_API_KEY=sk-ant-...
```

### 3. Run Database Migration

1. Go to [Supabase Dashboard](https://supabase.com/dashboard)
2. Open SQL Editor
3. Paste contents of `supabase/migrations/001_pantry_items.sql`
4. Click "Run"

### 4. Copy Files to Your Project

Copy the files to your MealMate project:

```bash
# From this folder, copy to your mealmate1 repo:
cp app/api/scan-food/route.js ../mealmate1/app/api/scan-food/
cp components/FoodScanner.jsx ../mealmate1/components/
cp hooks/useFoodScanner.js ../mealmate1/hooks/
cp lib/pantry.js ../mealmate1/lib/
```

## 🔧 Integration

### Basic Usage

```jsx
import FoodScanner from "@/components/FoodScanner";
import { addItemsToPantry } from "@/lib/pantry";

function MyPage() {
  const handleAddToPantry = async (items) => {
    const result = await addItemsToPantry(items, userId);
    if (result.success) {
      console.log(`Added ${result.added} items!`);
    }
  };

  return <FoodScanner onAddToPantry={handleAddToPantry} />;
}
```

### Using the Hook Directly

```jsx
import { useFoodScanner } from "@/hooks/useFoodScanner";

function CustomScanner() {
  const {
    isScanning,
    isCameraOpen,
    detectedItems,
    videoRef,
    canvasRef,
    openCamera,
    scanImage,
  } = useFoodScanner();

  return (
    <div>
      <video ref={videoRef} autoPlay playsInline />
      <canvas ref={canvasRef} hidden />
      <button onClick={openCamera}>Open Camera</button>
      <button onClick={() => scanImage(null, apiKey)}>Scan</button>
    </div>
  );
}
```

## 📊 Data Flow

```
┌─────────────┐     ┌──────────────┐     ┌─────────────┐
│   Camera    │────▶│  Capture     │────▶│  Base64     │
│   Feed      │     │  Image       │     │  Image      │
└─────────────┘     └──────────────┘     └─────────────┘
                                                │
                                                ▼
┌─────────────┐     ┌──────────────┐     ┌─────────────┐
│  Supabase   │◀────│  Parse       │◀────│  Claude     │
│  Pantry     │     │  Response    │     │  Vision API │
└─────────────┘     └──────────────┘     └─────────────┘
```

## 🔐 API Key Options

Users can provide their own Claude API key in two ways:

1. **Runtime**: Enter in the modal when first scanning
2. **Environment**: Set `CLAUDE_API_KEY` in `.env.local`

Keys are stored in `localStorage` for convenience.

## 📱 Features

- ✅ Camera access with front/back switch
- ✅ Photo upload support
- ✅ Real-time scanning feedback
- ✅ Item selection before adding
- ✅ Category & freshness detection
- ✅ Estimated expiry dates
- ✅ Mobile-optimized UI
- ✅ Supabase real-time sync

## 🎨 Customization

### Styling

The component uses Tailwind CSS. Override styles by wrapping in your own container or modifying the component directly.

### Categories

Edit the `categoryIcons` object in `FoodScanner.jsx`:

```js
const categoryIcons = {
  produce: "🥬",
  dairy: "🧀",
  // Add more...
};
```

### AI Prompt

Modify the Claude prompt in `app/api/scan-food/route.js` to change detection behavior.

## 🐛 Troubleshooting

| Issue | Solution |
|-------|----------|
| Camera not opening | Check browser permissions, use HTTPS |
| "API key required" | Add key via modal or environment variable |
| No items detected | Improve lighting, ensure food is visible |
| Slow scanning | Normal - Vision API takes 2-5 seconds |
| Items not saving | Check Supabase connection, run migration |

## 📄 License

Part of MealMate - www.bestmealmate.com
