# Project-Verna


# Getting Started with Expo + NativeWind (Tailwind CSS)

Setting up a React Native project with Tailwind CSS is traditionally more tedious than a web stack like React + Vite. This guide serves as a cheat sheet for configuring the tech stack from scratch and managing the mobile development workflow.

## Tech Stack

- **Framework**: [Expo](https://expo.dev/) (React Native)
- **Styling**: [NativeWind v4](https://www.nativewind.dev/) (Tailwind CSS for React Native)
- **Routing**: Expo Router (File-based routing)
- **Language**: TypeScript / TSX

---

## 1. Initial Setup Instructions

Follow these exact steps to configure NativeWind v4 in a new Expo project.

**1. Install Dependencies**
```bash
npm install nativewind tailwindcss react-native-reanimated react-native-safe-area-context
```

**2. Initialize Tailwind**
```bash
npx tailwindcss init
```

**3. Configure `tailwind.config.js`**
Update the generated file to include the NativeWind preset and your content paths:
```javascript
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: [
    "./app/**/*.{js,jsx,ts,tsx}",
    "./components/**/*.{js,jsx,ts,tsx}",
  ],
  presets: [require("nativewind/preset")],
  theme: {
    extend: {},
  },
  plugins: [],
};
```

**4. Create `global.css`**
Create a `global.css` file at the root of your project:
```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

**5. Update `metro.config.js`**
Wrap your Metro config to process the Tailwind classes:
```javascript
const { getDefaultConfig } = require("expo/metro-config");
const { withNativeWind } = require('nativewind/metro');

const config = getDefaultConfig(__dirname);

module.exports = withNativeWind(config, { input: './global.css' });
```

**6. Update `babel.config.js`**
```javascript
module.exports = function (api) {
  api.cache(true);
  return {
    presets: [
      ["babel-preset-expo", { jsxImportSource: "nativewind" }],
      "nativewind/babel",
    ],
  };
};
```

**7. Fix TypeScript Errors (`className`)**
To stop TypeScript from complaining about the `className` prop on native elements, create a `nativewind-env.d.ts` file in the root directory:
```typescript
/// <reference types="nativewind/types" />
```

**8. Import Global CSS**
In your root layout (`app/_layout.tsx`), import the CSS file and use a `<Slot />` so Tailwind applies to all child screens:
```tsx
import { Slot } from "expo-router";
import "../global.css";

export default function RootLayout() {
  return <Slot />;
}
```

---

## 2. The Development Workflow

Mobile development requires different testing environments depending on the libraries you use. Follow this 4-phase workflow.

### PHASE 1: UI Development (Fast Iteration)
Build your UI first before adding any heavy custom native libraries (like ML models).
* **Tool:** Expo Go (App from Play Store)
* **Command:** `npx expo start`
* **How it works:** Scan the QR code. Changes update instantly over the network. No compiling required.

### PHASE 2: Switching Over (The One-Time Transition)
Once your UI is ready and you need to add custom native code (e.g., TensorFlow Lite, specific camera modules), Expo Go will no longer work.
1. Install your native packages: `npm install your-native-library`
2. Install the dev client: `npm install expo-dev-client`
3. Build your custom APK in the cloud using EAS:
   ```bash
   npm install -g eas-cli
   eas build --profile development --platform android
   ```
4. Download the resulting `.apk` and install it on your physical phone. This app permanently replaces Expo Go for your project.

### PHASE 3: Native Development
You are now developing with your custom native code embedded in the app.
* **Tool:** Your Custom Dev Client APK
* **Command:** `npx expo start`
* **How it works:** Open your custom app on your phone. It connects to the Metro bundler just like Expo Go. UI and JS changes still update instantly!

### PHASE 4: Adding More Native Libraries
* **Trigger:** You need to add *another* new native package later.
* **Action:** Run `eas build` again to generate a new APK, reinstall it on your phone, and return to Phase 3.



