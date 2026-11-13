This is a [Next.js](https://nextjs.org) project bootstrapped with [`create-next-app`](https://nextjs.org/docs/app/api-reference/cli/create-next-app)..

## Getting Started

First, run the development server:

```bash
npm run dev
# or
yarn dev
# or
pnpm dev
# or
bun dev
```

Open [http://localhost:3000](http://localhost:3000) with your browser to see the result.

You can start editing the page by modifying `app/page.tsx`. The page auto-updates as you edit the file.

This project uses [`next/font`](https://nextjs.org/docs/app/building-your-application/optimizing/fonts) to automatically optimize and load [Geist](https://vercel.com/font), a new font family for Vercel.

## Learn More

To learn more about Next.js, take a look at the following resources:

- [Next.js Documentation](https://nextjs.org/docs) - learn about Next.js features and API.
- [Learn Next.js](https://nextjs.org/learn) - an interactive Next.js tutorial.

You can check out [the Next.js GitHub repository](https://github.com/vercel/next.js) - your feedback and contributions are welcome!

## Deploy on Vercel

The easiest way to deploy your Next.js app is to use the [Vercel Platform](https://vercel.com/new?utm_medium=default-template&filter=next.js&utm_source=create-next-app&utm_campaign=create-next-app-readme) from the creators of Next.js.

Check out our [Next.js deployment documentation](https://nextjs.org/docs/app/building-your-application/deploying) for more details.

## Social features

The application stores account privacy, follow requests, saved posts, stories, notifications, and content views in MySQL through Prisma. New social features use the same persistent database and authenticated server actions:

- Account username, password, and privacy settings are available at `/settings`.
- Direct conversations are available at `/messages`; profile-level message controls start a private conversation.
- Story and post audiences support everyone, followers, close friends, and selected usernames.
- Photo posts support up to 10 images, with ordering and preview controls before publishing and swipeable carousels in the feed and post detail.
- Saved posts and archived posts are available at `/saved` and `/archive`.
- Blocking, profile controls, repost permissions, pinning, highlighting, and story/reel view statistics are enforced on the server.
- DM photos and profile pictures use private media URLs; account owners can adjust avatar zoom and position from their profile.
- Stories retain their media and view records in the owner's private archive after the 24-hour expiry. The Node server also archives expired stories periodically; Vercel deployments use the configured minute cron and require a `CRON_SECRET` environment variable.
- Feed and Explore recommendations use first-party likes, comments, saved/reposted posts, follows, explicit interests, recent search terms, and recorded video watch time. Recommendation settings and “not interested” feedback are available from `/settings/interests`; watch-history removal hides personal history without deleting creator view totals.
- Explore searches Wikimedia Commons' public no-key catalog and Internet Archive's RSS search feed. Results are filtered to supported Creative Commons/Public Domain license metadata and linked to their source; Commons media is played from Wikimedia and Archive films use the Archive player. Neither needs credentials; no scraping or downloading/rehosting is used. YouTube remains an optional source and requires server-only `YOUTUBE_DATA_API_KEY`.
- Story music uses Apple catalog track previews, not stored full songs. Playback starts only after a viewer taps the story music label. Music choices, segment offsets, and label layout can be edited while a story is active.
- New direct conversations start as message requests. Only the conversation participants can see shared song titles and status notes; shared song cards do not auto-play audio.

For a database that already contains the earlier migrations, move existing uploads out of the public directory, apply pending migrations, and regenerate the Prisma client:

```bash
npm run media:secure
npx prisma migrate deploy
npx prisma generate
```

Run the database-backed access-control smoke test (it runs inside a rolled-back transaction):

```bash
npm run db:test-social-access
```

The migration updates stored post/story URLs to `/media/...`; the route serves those files only after checking the signed-in viewer's current content permissions. New uploads are stored outside `public/`.
