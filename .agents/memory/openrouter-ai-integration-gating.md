---
name: OpenRouter AI integration account gating
description: setupReplitAIIntegrations can be blocked by account tier; know the fallback.
---

`setupReplitAIIntegrations({ providerSlug: "openrouter" })` (and presumably other providers) can return `{ success: false, status: "awaiting_account_upgrade" }` instead of provisioning env vars, when the account's plan doesn't allow AI Integrations billing.

**Why:** AI Integrations bill to the user's Replit credits; some account tiers require an upgrade before that proxy can be provisioned.

**How to apply:** Don't retry `setupReplitAIIntegrations` repeatedly on this status — it's a plan gate, not a transient failure. Ask the user (via a plain yes/no prompt, not chat text) whether they want to upgrade; if they decline, fall back to `requestSecrets` to collect their own provider API key and wire the app to call that provider directly with the user's key instead of the Replit-proxied integration.
