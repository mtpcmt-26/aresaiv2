# Ares

“Create an AI chatbot app called Ares with 5 selectable modes: Normal, 0‑Filter, People‑Pleaser, Political, and Short. Ares must always tell the truth, never flatter the user, never emotionally validate the user, never apologize unnecessarily, and never act like a people‑pleaser unless in People‑Pleaser mode. Ares should be confident, direct, and assertive.

MODE BEHAVIOR RULES

1. Normal Mode

Direct, logical, factual

Neutral tone

No emotional padding

No flattery

No unnecessary politeness

Full explanations allowed

2. 0‑Filter Mode (AGE‑GATED, 13+ ONLY)

Before enabling this mode, require the user to confirm they are 13 or older

If the user is under 13, block the mode and show:
“0‑Filter mode is restricted to users 13+. Please choose another mode.”

Behavior:

Brutally honest

No sugarcoating

No politeness

No softening

Profanity allowed (mild–moderate, never hateful, never targeting people)

Attacks ideas, never individuals

Still safe and non‑harmful

3. People‑Pleaser Mode

Soft, gentle, encouraging tone

Still tells the full truth

No lying

No personal flattery

Uses positive framing

Structure:

Start with something positive

Deliver the truth

End with a supportive suggestion

4. Political Mode

Analytical, confident, issue‑focused

Can take a reasoned political side, including positions on candidates or parties
Can make clearly labeled forecasts when asked
Tone: blunt, assertive, debate‑style
Must distinguish evidence from opinion and avoid fabricating claims

5. Short Mode

1–2 sentence answers

Direct and concise

No fluff

GLOBAL RULES FOR ARES

Never act like a people‑pleaser unless in People‑Pleaser mode

Never flatter the user

Never emotionally validate the user

Never apologize unless Ares actually made a factual mistake

Never say “As an AI…”

Always be confident and truthful

No moralizing

No excessive politeness

No hedging unless uncertainty is real

Emoji use is controlled by the Use emojis setting (on by default)
Profanity is ONLY allowed in 0‑Filter mode

Ares can discuss any topic. It does not provide actionable instructions for illegal activity or highly unsafe acts that could seriously harm people. The 0‑Filter mode changes personality, not the content boundary.

APP FEATURES

Mode selector UI (buttons or dropdown)

Age confirmation step when selecting 0‑Filter mode

Chat interface with message history

Ares responds according to the selected mode

Store selected mode in state so it persists during the session

Clean, minimal UI with dark mode option

Implement all of the above.”

This project was built with [Lovable](https://lovable.dev).

**Live app**: https://aresai.lovable.app

## Build with Lovable

Continue developing this project in the [Lovable editor](https://lovable.dev/projects/c446a333-97c1-4035-92b3-1cbbecb4b891).

- **Ship faster**: describe what you want to build and Lovable handles the code.
- **Stay in sync**: every change made in Lovable is committed straight to this repository.
- **Full ownership**: this code is yours. Push to `main` on GitHub and your changes sync back into Lovable, ready for your next prompt.

## Development

Prefer working locally? You need Node.js and npm — [install with nvm](https://github.com/nvm-sh/nvm#installing-and-updating).

```sh
git clone <this-repository-url>
cd <repository-name>
npm i
npm run dev
```

### Supabase function secrets

Video generation requires `REPLICATE_API_TOKEN` as a Supabase Edge Function secret. Set it in the Supabase Dashboard's Edge Function Secrets settings, then deploy the `video-gen` function. Never put the token in `.env`, frontend code, or a `VITE_` variable. If a token has been shared in chat or committed, revoke it and configure a replacement.

Google Gemini models use Google's Gemini API directly and require `GOOGLE_AI_API_KEY` as a server-side Edge Function secret. OpenRouter models, including Claude in normal chat and Research, require `OPENROUTER_API_KEY`. Configure replacement keys in Supabase Edge Function Secrets, then deploy the updated `chat`, `ares-api`, `research`, `quiz-gen`, and `memory` functions. Never place provider keys in frontend environment variables or source files. Revoke any provider key shared in chat and use a newly generated secret.

### Guest chat

Guest chat allows 50 messages per public IP per UTC calendar month. The server stores only a SHA-256 hash of the request IP for quota checks. Apply the `guest_chat_message_limits` migration and deploy the updated `chat` function with JWT gateway verification disabled as configured in `supabase/config.toml`. Guest conversations are stored in the current browser; signing in unlocks account features and image generation. Guests sharing a public IP also share the same monthly allowance.

### Research library, file Q&A, and Ares Memory

Apply `20261011190000_research_history.sql`, `20261011200000_research_collections.sql`, `20261011210000_user_memory.sql`, and `20261011220000_research_spaces.sql` in order, then deploy the updated `research`, `discover`, `chat`, and `memory` Edge Functions. Search history and personal collections are private to the signed-in account. Users can reopen or delete past research, organize it into collections or private Spaces with custom answer instructions, expand or collapse source details, and continue with suggested follow-up questions. Completed answers can be copied or exported as Markdown with their source links. Firecrawl credit or rate-limit failures use DuckDuckGo search, with Google News RSS added for news-focused requests.

Research accepts PDF, TXT, Markdown, CSV, and DOCX files up to 10 MB. Text is extracted in the browser and sent with the question to the selected AI model; Ares does not store the original file. PDFs must have selectable text (scanned PDFs requiring OCR are not supported), and extracted content is capped at 24,000 characters. Voice input and answer playback use browser speech APIs where supported.

Research also offers a curated set of OpenRouter models. To enable them, add `OPENROUTER_API_KEY` as a Supabase Edge Function secret (never as a frontend or `VITE_` variable), then deploy the updated `research` function. OpenRouter is used for the selected model's answer, Deep Research query planning, and follow-up suggestions. If an API key has been shared in chat, revoke it and configure a replacement.

Ares Memory is enabled by default for signed-in accounts; users can turn it off in Settings or inspect and delete saved memories. Guests do not have memory. Memory extraction sends the latest chat exchange to the configured AI gateway and stores only eligible concise facts; it is not backfilled from earlier chats.

Apply `20261011230000_user_emoji_preference.sql` to sync the emoji setting across signed-in devices. UI settings are also cached locally, and older schemas retain local settings until updated.

### Google sign-in

Google sign-in uses Supabase OAuth first and falls back to the Lovable OAuth broker when the Supabase Google provider is not enabled. For Supabase OAuth, enable Google under Authentication → Providers and allow the app's exact origin in the Supabase redirect URL settings and the Google OAuth client. The Lovable broker must also be available on the deployed Lovable origin for fallback sign-in.

### Ares Labs

Labs offers only GPT-5.2, GPT-5, Kimi K2, Qwen3 32B, and Llama 3.3 70B for code generation; the Edge Function enforces the same model allowlist. Build requests are managed above the route level so they continue while navigating between Ares pages. Completed previews, project source files, and chat turns are saved with the project in browser storage, and a completion toast links back to that project's preview. An active-build indicator appears in the Labs header and project sidebar.

Labs accepts up to five PDF, DOCX, TXT, Markdown, CSV, or common source-code files per build (10 MB each; extracted text is capped at 60,000 characters total). Generated projects include a runnable iframe preview and may include a full-stack source scaffold (frontend, backend/API routes, schema, and setup instructions). The preview has no provisioned backend or secrets; configure and run generated server code in your own environment. Use Export project to download the preview and source files as JSON.
