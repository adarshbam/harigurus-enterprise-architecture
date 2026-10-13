# 04 — ProCodrr TypeScript LMS & Cubes 3D WebXR Engineering

## 1. ProCodrr EdTech LMS (`procodrr-frontend`)
- **Stack:** Next.js App Router, **Strict TypeScript**, TailwindCSS, `@svgr/webpack`, Husky, Lint-Staged, Airbnb TypeScript ESLint.
- **Key Engineering Contributions (13 Commits):**
  - **Data Normalization (`commit b999aca`):** Flattened deeply nested course-chapter-episode trees into O(1) lookup structures typed via `src/app/types/course.ts` and `episode.ts`.
  - **Interactive Video Streaming (`src/app/[course]/[episode]/page.tsx`):** Built responsive episode navigation drawers, YouTube embed players (`YoutubeEmbed.tsx`), and completion progress trackers (`ProgressBar.tsx`, `ProgressCircle.tsx`).
  - **React Portal Search Modal (`SearchPopup.tsx` & `Portal.tsx`):** Built a keyboard-accessible command-palette search modal mounted outside the main DOM hierarchy via React Portals.

---

## 2. Cubes — 3D & Augmented Reality Platform (`cubes`)
- **Stack:** React, Google `<model-viewer>` (WebGL / WebXR), `.glb` 3D Binary Assets, `.hdr` Skybox Lighting, Express.js, MongoDB.
- **Key Engineering Contributions (6 Commits):**
  - **3D & AR Rendering Engine (`ViewModel.js`):** Configured `<model-viewer>` with High Dynamic Range environment maps (`Platformer Character BG.hdr`, `spruit_sunrise_1k_HDR.hdr`), skeletal animations (`CharacterArmature|Walk`), camera pan/orbit controls, and mobile AR projection (`ar-modes="webxr scene-viewer quick-look"`).
  - **Asset Upload & Social Feed (`UploadModel.js`, `Post.js`, `PostsList.js`):** Built the multi-part 3D `.glb` file upload interface and interactive social showcase feed.
