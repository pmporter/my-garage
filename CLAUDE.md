# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**My Garage** is a comprehensive vehicle maintenance tracking and management application built with React Native Expo and TypeScript. The app is designed for owners of motorcycles, cars, bikes, boats, and other vehicles that require regular maintenance and record keeping.

### Core Purpose
The application combines maintenance tracking with intelligent affiliate marketing, providing users with precise part recommendations at optimal timing. For example, when a user's 2024 Aprilia Tuono V4 is approaching an oil change, the app will show the exact oil filter needed with affiliate links to Amazon, RevZilla, and other partners.

### Business Model
- **Primary Revenue**: Affiliate commissions from targeted part recommendations
- **Secondary Revenue**: Premium features, targeted advertising, data insights
- **Key Differentiator**: Hyper-targeted recommendations (specific parts for specific vehicles at optimal timing)

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

## Application Features

### Core Features
- **Multi-Vehicle Support**: Motorcycles, cars, trucks, boats, bikes, ATVs, etc.
- **Maintenance Tracking**: Oil changes, services, repairs with photo documentation
- **Smart Scheduling**: Automatic reminders based on time/mileage intervals
- **Document Management**: Receipts, warranties, manuals with OCR processing
- **Affiliate Marketing**: Contextual part recommendations with multi-partner integration

### Key User Flows
1. **Vehicle Registration**: Add vehicle → Auto-populate specs via VIN → Set maintenance schedule
2. **Maintenance Logging**: Record service → Upload photos/receipts → Track parts used
3. **Smart Recommendations**: Maintenance due → Show specific parts → Affiliate purchase links
4. **Analytics**: Cost tracking, service history, vehicle health scoring

## Technical Architecture

### Frontend Stack
- **React Native 0.79.5** with **React 19.0.0**
- **Expo SDK ~53.0.20** with Router v5.1.4
- **TypeScript** for type safety
- **Cross-platform**: iOS, Android, Web support

### Backend Architecture (Planned)
- **GraphQL API** with Apollo Server
- **Node.js/Express** backend services
- **PostgreSQL** for structured data
- **Redis** for caching and sessions
- **AWS S3** for photo/document storage

### Key Integrations
- **VIN Lookup APIs** for vehicle specifications
- **Affiliate APIs**: Amazon Associates, RevZilla, AutoZone
- **OCR Services** for receipt processing
- **Push Notifications** via Firebase FCM

## Project Structure

### Current State (Expo Template)
This is currently a fresh Expo template with basic tab navigation. The production application will be built on this foundation.

### Documentation
Comprehensive documentation is available in the `/docs` directory:
- `docs/overview.md` - Application overview and business model
- `docs/features.md` - Detailed feature specifications
- `docs/architecture.md` - System architecture and technical design
- `docs/data-models.md` - Database schema and data models
- `docs/api/api-specification.md` - Complete GraphQL API specification

### Development Notes

- All components follow theme-aware patterns using the custom theming hooks
- Platform-specific styling is handled via `Platform.select()` where needed
- Tab bar uses blur effects on iOS with transparent positioning
- Path mapping configured with `@/*` for clean imports
- ESLint configured with Expo's flat config

## Specialized Agents

The project includes specialized Claude agents for development assistance:
- **react-native-security-expert**: Security-focused code review and implementation guidance
- **react-native-architect**: System design, technical documentation, and requirements analysis