<!-- LOVABLE:BEGIN -->
> [!IMPORTANT]
> This project is connected to [Lovable](https://lovable.dev). Avoid rewriting
> published git history — force pushing, or rebasing/amending/squashing commits
> that are already pushed — as it rewrites history on Lovable's side and the
> user will likely lose their project history.
>
> Commits you push to the connected branch sync back to Lovable and show up in
> the editor, so keep the branch in a working state.

## What Lovable manages

Rules outside this block are the project's own. Change the pieces below in
Lovable, not by hand. With the [Lovable MCP](https://mcp.lovable.dev)
connected, `send_message` asks Lovable to do it; otherwise ask the user to
use the editor.

- **Database.** Do not add or edit `supabase/migrations/` or `drizzle/`.
  Ask Lovable to change the schema.
- **Generated files.** Do not edit a file whose header says it is automatically
  generated.
- **AI gateway.** Keep model calls on Lovable's gateway, and keep
  `LOVABLE_API_KEY` out of browser code.
- **Connectors.** Lovable provides native connectors. `list_connectors` shows
  which are enabled; `add_connector` only returns the dashboard link. When a
  connector is enabled, calls go through the connector gateway and need
  `LOVABLE_API_KEY`. When Lovable does not provide one, users can bring their
  own key and store it in Secrets.
- **Secrets.** Lovable manages them. Do not put secret values in the repo.
<!-- LOVABLE:END -->

## Project rules

- All backend access lives in `src/services/` (`apiClient.ts` for auth/AI/finder, `storageService.ts` for the cloud-backed store); UI never calls the database or localStorage directly — keeps pages free of data plumbing.
- Gemini is called only from server functions (`ai.functions.ts` → `gemini.server.ts`) with the user's own `GEMINI_API_KEY`; `aiNotice.ts` normalises model JSON and is covered by `src/test/ai-notice.test.ts` — never trust raw model output.
- New users get the sample notices copied into their own rows once (`profiles.samples_seeded`), so every notice and step is a real row whose ticks persist.
- One-Click Demo Login uses a public server function that signs into a single fixed demo account (password in `DEMO_ACCOUNT_PASSWORD`) and resets its profile — keep it scoped to that account only.
- Components read state through `src/hooks/use-app-store.ts` (`useSyncExternalStore` over the in-memory cloud store; preferences over localStorage); writes are optimistic then saved — keeps every page in sync and SSR-safe.
- Workspace pages live under the `_workspace` pathless layout, which renders only after hydration once the auth status is `ready` — avoids hydration mismatches and redirect flashes.
- Sample notice dates are generated relative to today — so the demo always shows urgent/approaching/safe deadlines.
- Deadline colour and eligibility rules are covered by `src/test/rules.test.ts`; update those tests when the rules change.
- The light theme is the `:root` default and `.night` on `<html>` swaps tokens (not `.dark`, so shadcn `dark:` variants never activate); use semantic tokens only, never `dark:` variants.
