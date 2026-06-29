# AMI — RESTART / ORIENTATION

Read this first if a chat closed, or you're picking AMI back up cold.
Keep this file in the repo root next to `Index.html`.

---

## RULE 0 — TEST ON THE LIVE SITE, NEVER A DOWNLOAD

The #1 time-waster: testing AMI from a downloaded file
(`C:/Users/HUSSIEN/Downloads/index (N).html`).
That file is a frozen old copy. Edits committed to GitHub will NEVER show there.

**Always test here:**
```
https://hnsaad1973-tech.github.io/Ami/Index.html
```
Get the exact URL from: GitHub repo → Settings → Pages → "Visit site".

If a fix "didn't work," CHECK THE URL BAR FIRST.
- `github.io/...`  → live, correct.
- `C:/Users/...Downloads/...` → old file. Switch tabs and retest.

Delete old `index (1).html` … `index (5).html` from Downloads so you can't open them by accident.

---

## HOW AMI IS WIRED (the working setup)

- **Front end:** single `Index.html`, GitHub Pages, repo `hnsaad1973-tech/Ami`, branch `main`.
- **Brain:** Claude, called through a Supabase **Edge Function named `ai`** (server-side proxy).
- The Anthropic key lives ONLY in Supabase secrets (`ANTHROPIC_API_KEY = sk-ant-...`). Never in `Index.html`.

### The two lines that matter in Index.html

**Line ~259 — the fetch headers. Use `apikey` ONLY. No `Authorization`.**
```js
headers:{"Content-Type":"application/json","apikey":state.cfg.anon}
```
If you put back `"Authorization":"Bearer "+state.cfg.anon` you get **HTTP 401** —
because `sb_publishable_` anon keys are NOT JWTs and the gateway rejects them.

**Line ~267 — reading the reply. The `ai` function returns `{reply, data}`.**
```js
return (data.reply || "(no reply)")
```
The function already flattens the text into `data.reply`.
If you read `data.content` at the top level instead, you get an **empty box** (no words).

### Supabase `ai` function settings
- **Verify JWT: OFF.** (On = 401/426.)
- Secret `ANTHROPIC_API_KEY` set to the current `sk-ant-...` key.
- After changing a secret, **redeploy** the function or it won't take effect.

---

## SYMPTOM → CAUSE (fast triage)

| Symptom | Most likely cause | Fix |
|---|---|---|
| HTTP 401, "Turn OFF Verify JWT" | Running OLD Downloads file, OR `Authorization` header present | Use live URL; line 259 = `apikey` only |
| Empty box / dash, no words | Front end reading wrong field | Line 267 = `data.reply` |
| 401 even on live site, header correct | Anthropic key wrong/missing in Supabase | Set `ANTHROPIC_API_KEY`, redeploy `ai` |
| Nothing changes after commit | Stale cache, or wrong file | Hard-refresh (Ctrl+Shift+R / reopen PWA); check URL |

---

## DEPLOY CHECKLIST (after any Index.html edit)

1. Edit in GitHub editor → **Commit changes** → directly to `main`.
2. Wait 1–2 min (Pages rebuild).
3. **Hard-refresh** the live site (Ctrl+Shift+R; on iPhone, close PWA fully and reopen).
4. Test on `github.io/Ami/Index.html` — type "say hello" → SEND.
5. Confirm real text appears.

---

## GOLDEN RULE

Test data → verify → real data. Never load real Leone Investment data before verification.

## OWNERSHIP

Master accounts (GitHub, Supabase, Anthropic, Vercel) = Chairman. Developer is collaborator only.
