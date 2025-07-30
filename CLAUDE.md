# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a React Native Expo application built with TypeScript, using Expo Router for file-based navigation and a tab-based layout. The project supports cross-platform development (iOS, Android, Web) with automatic light/dark mode theming.

## Development Commands

- `npm start` or `npx expo start` - Start the development server
- `npm run android` - Run on Android emulator/device  
- `npm run ios` - Run on iOS simulator/device
- `npm run web` - Run in web browser
- `npm run lint` - Run ESLint for code quality checks
- `npm run reset-project` - Reset to blank project (moves current app to app-example)

## Architecture

### File-Based Routing
The app uses Expo Router with file-based routing:
- `app/_layout.tsx` - Root layout with theme provider and Stack navigator
- `app/(tabs)/_layout.tsx` - Tab navigation layout with Home and Explore tabs
- `app/(tabs)/index.tsx` - Home screen (default route)
- `app/(tabs)/explore.tsx` - Explore/second tab screen
- `app/+not-found.tsx` - 404 screen

### Theming System
The project implements a comprehensive theming system:
- `constants/Colors.ts` - Defines light/dark color palettes
- `hooks/useColorScheme.ts` - Re-exports React Native's useColorScheme hook
- `hooks/useThemeColor.ts` - Custom hook that resolves theme colors based on current scheme
- `components/ThemedText.tsx` - Text component with automatic theme color application
- `components/ThemedView.tsx` - View component with automatic background theming

### Component Architecture
- **Themed Components**: `ThemedText` and `ThemedView` automatically adapt to light/dark modes
- **UI Components**: Located in `components/ui/` (IconSymbol, TabBarBackground)
- **Interactive Components**: HapticTab provides haptic feedback, Collapsible for expandable content
- **Layout Components**: ParallaxScrollView for scrolling with parallax header effects

### Key Features
- **Cross-Platform Support**: Configured for iOS, Android, and web with platform-specific optimizations
- **New Architecture Enabled**: Uses React Native's new architecture (`newArchEnabled: true`)
- **TypeScript Path Mapping**: `@/*` paths map to project root for clean imports
- **Expo Router**: File-based routing with typed routes enabled
- **Custom Fonts**: SpaceMono font loaded via expo-font
- **Haptic Feedback**: Tab interactions include haptic feedback on iOS
- **Expo Image**: Optimized image component for better performance

### Configuration Files
- `app.json` - Expo configuration with platform-specific settings, splash screen, and plugins
- `tsconfig.json` - TypeScript config extending Expo's base with strict mode and path mapping
- `eslint.config.js` - ESLint using Expo's flat config with dist folder ignored

## Development Notes

- The project uses React Native 0.79.5 with React 19.0.0
- Expo SDK ~53.0.20 with Router v5.1.4
- All components follow theme-aware patterns using the custom theming hooks
- Platform-specific styling is handled via `Platform.select()` where needed
- Tab bar uses blur effects on iOS with transparent positioning