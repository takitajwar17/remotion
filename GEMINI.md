# Project Context: Remotion + Next.js Template

## Project Overview
This project is a **Next.js application** integrated with **Remotion** for creating programmatic videos. It serves as a template that includes the `@remotion/player` for embedding videos in the web app and `@remotion/lambda` for serverless rendering on AWS.

## Technology Stack
*   **Framework:** Next.js 16 (App Router)
*   **Video Engine:** Remotion 4.0
*   **Language:** TypeScript
*   **Styling:** Tailwind CSS v4
*   **Validation:** Zod
*   **Deployment:** Vercel (Web App) / AWS Lambda (Video Rendering)

## Key Directories & Files

### `src/`
*   **`src/app/`**: Contains the Next.js App Router pages and API routes. This is the web interface where users might interact with the player or trigger renders.
*   **`src/remotion/`**: Contains the core video logic.
    *   **`Root.tsx`**: The main entry point where Remotion `Composition`s are registered. This is where you define the video metadata (FPS, dimensions, duration).
    *   **`MyComp/`**: Example directory containing video components (e.g., `Main.tsx`, `NextLogo.tsx`).
*   **`src/lambda/`**: Contains code related to the AWS Lambda functions for rendering.
*   **`src/components/`**: Shared React components used in the Next.js app (UI elements).

### Configuration
*   **`remotion.config.ts`**: Configuration for the Remotion CLI and Studio (e.g., webpack overrides, image formats).
*   **`deploy.mjs`**: Script to deploy the Remotion bundle and Lambda function to AWS.
*   **`config.mjs`**: Configuration settings for the AWS Lambda deployment (RAM, timeout, region).
*   **`tailwind.config.js`** / **`postcss.config.mjs`**: Tailwind CSS configuration.

## Key Commands

### Development
*   **`npm run dev`**: Starts the Next.js development server (accessible at `http://localhost:3000`).
*   **`npm run remotion`**: Opens the Remotion Studio (accessible at `http://localhost:3001`) to preview and debug videos in isolation.

### Rendering & Deployment
*   **`npm run render`**: Renders the video locally using the Remotion CLI.
*   **`npm run deploy`**: Deploys the Remotion project to AWS Lambda.
*   **`npm run lint`**: Runs ESLint to check for code quality issues.

## Development Conventions
*   **Separation of Concerns:** The Next.js app (`src/app`) and Remotion video logic (`src/remotion`) are kept separate but share the same repository and dependencies.
*   **Compositions:** New videos should be registered as `Composition` components in `src/remotion/Root.tsx`.
*   **Styling:** Tailwind CSS is configured for use in both the Next.js app and Remotion videos.
*   **Lambda:** Changes to `config.mjs` or the video template require a re-deploy using `npm run deploy`.
