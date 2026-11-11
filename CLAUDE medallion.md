# Medallion: rules for every session

1. Read SPEC.md before any task. It is the source of truth.
2. Build one phase at a time (SPEC section 12.1). Stop at the phase gate and summarize.
3. Write the acceptance tests for the phase (SPEC 13.4) first. A phase is done only when they all pass.
4. Scoring: port the reference function in SPEC 3.2 to TypeScript exactly. Never invent scoring.
5. Security: score only on the server. Never send answer sheets, shares, counts or medal answers
   for an unsettled day to the browser. Prompt text only comes from the start endpoint.
6. Game day = the date in America/New_York. Store timestamps in UTC.
7. Use the endpoints in SPEC 13.2 and routes in SPEC 13.3 exactly. If something is missing,
   propose the addition to SPEC.md before coding it.
8. All tunable values (timer, sigma, k, thresholds) come from SPEC 12.4 and live in one config file.
9. Stack: Next.js App Router, TypeScript, Tailwind, Supabase, Resend, Stripe, Anthropic API.
   No other libraries unless clearly needed; say why when you add one.
10. UI copy: never use em dashes. Use the theme words: strike, mint, the med, vault.
