# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Next.js application template integrated with Remotion for programmatic video creation. The project combines a web application frontend with serverless video rendering capabilities using AWS Lambda.

**Stack**: Next.js 16 (App Router), Remotion 4.0, TypeScript, Tailwind CSS v4, Zod validation

## Development Commands

### Essential Commands
- `npm run dev` - Start Next.js development server (localhost:3000)
- `npm run remotion` - Open Remotion Studio for video preview/debugging (localhost:3001) 
- `npm run build` - Build Next.js application
- `npm run lint` - Run ESLint on entire codebase
- `npm run render` - Render video locally using Remotion CLI
- `npm run deploy` - Deploy Remotion bundle and Lambda function to AWS

### Testing Commands
No test framework is configured in this project.

## Architecture & Key Directories

### Core Architecture
The project separates concerns between web application (`src/app/`) and video logic (`src/remotion/`):

- **Web App**: Next.js app providing UI for video preview and rendering controls
- **Video Engine**: Remotion compositions that define video content and animations
- **Lambda Integration**: AWS Lambda functions for serverless video rendering

### Directory Structure
```
src/
├── app/                    # Next.js App Router (web interface)
│   ├── api/lambda/        # API routes for Lambda rendering
│   └── page.tsx           # Main page with video player
├── remotion/              # Video compositions and logic
│   ├── Root.tsx          # Composition registry (ENTRY POINT)
│   ├── MyComp/           # Video components directory
│   ├── index.ts          # Remotion registration file
│   └── webpack-override.mjs
├── components/            # Shared React components (UI)
├── helpers/              # API utilities and hooks
└── lambda/               # Lambda function code

types/                     # TypeScript definitions
├── constants.ts          # Video dimensions, FPS, composition props
└── schema.ts             # Zod schemas for API requests

config.mjs                # AWS Lambda deployment configuration
remotion.config.ts        # Remotion CLI configuration
deploy.mjs                # AWS deployment script
```

## Remotion-Specific Patterns

### Composition Registration
All video compositions must be registered in `src/remotion/Root.tsx`:

```tsx
<Composition
  id="MyComp"
  component={Main}
  durationInFrames={DURATION_IN_FRAMES}
  fps={VIDEO_FPS}
  width={VIDEO_WIDTH}
  height={VIDEO_HEIGHT}
  defaultProps={defaultMyCompProps}
/>
```

### Video Constants
Video specifications are centralized in `types/constants.ts`:
- `DURATION_IN_FRAMES`, `VIDEO_WIDTH`, `VIDEO_HEIGHT`, `VIDEO_FPS`
- `CompositionProps` schema defines input parameters for videos
- `defaultMyCompProps` provides default values

### Component Patterns
- Use `AbsoluteFill` for full-screen containers
- Use `Sequence` for timeline-based animations with `from` and `durationInFrames`
- Use `useCurrentFrame()` and `useVideoConfig()` for frame-based animations
- Use `spring()` for physics-based animations
- Load fonts with `@remotion/google-fonts`

### Animation Timing
- Calculate timing based on `fps` from `useVideoConfig()`
- Example: `const transitionStart = 2 * fps` for 2-second delay
- Use frame numbers, not time-based values

## AWS Lambda Configuration

### Environment Setup
1. Copy `.env.example` to `.env`
2. Set `REMOTION_AWS_ACCESS_KEY_ID` and `REMOTION_AWS_SECRET_ACCESS_KEY`
3. Configure Lambda settings in `config.mjs`:
   - `REGION`, `RAM`, `DISK`, `TIMEOUT`, `SITE_NAME`

### Deployment Workflow
1. **Initial setup**: Run `npm run deploy` to create Lambda function and S3 bucket
2. **After changes**: Re-run `npm run deploy` when:
   - Video template is modified
   - `config.mjs` is updated
   - Remotion version is upgraded

### Lambda Rendering
- API endpoint: `POST /api/lambda/render`
- Progress tracking: `POST /api/lambda/progress`
- Videos render with `framesPerLambda: 10` for parallel processing

## ESLint Configuration

The project uses a sophisticated ESLint setup with different rules for different contexts:
- **Next.js rules** applied to app code (`src/app/**`, `src/components/**`)
- **Remotion rules** applied only to video code (`src/remotion/**`)
- **Next.js rules disabled** within Remotion files to avoid conflicts

## Code Conventions

### File Organization
- Keep web app logic in `src/app/` and `src/components/`
- Keep video logic strictly in `src/remotion/`
- Shared types go in `types/`
- Utility functions in `src/helpers/`

### Styling
- Tailwind CSS v4 is available in both Next.js and Remotion contexts
- Use `className` prop for styling
- Inline styles take precedence over Tailwind for the Remotion Player component

### Type Safety
- All video props must extend the `CompositionProps` Zod schema
- Lambda API requests/responses use typed schemas in `types/schema.ts`
- TypeScript strict mode enabled with `noUnusedLocals`

## Important Notes

- **Webpack Override**: `src/remotion/webpack-override.mjs` customizes the build process
- **No Tests**: This template doesn't include a testing framework
- **Font Loading**: Use `@remotion/google-fonts` for consistent font loading
- **Image Format**: Configured to use JPEG for video frames (`remotion.config.ts`)
- **Vercel Deployment**: The `vercel.json` runs `deploy.mjs && next build` on deploy