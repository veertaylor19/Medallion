# Medallion: Master Plan

Nov 5, 2026 · @Tails

## 1. Concept and vision

Medallion is a free daily trivia game where you win by naming the answer in the middle of the crowd: not the most common, not the rarest, but the one that sits at the median of what everyone else said.

**The core mechanic.** Seven open-ended prompts a day ("Name a pasta dish"), the same for everyone. Every answer is scored by how close it lands to the median of all player responses. The crowd's answers are the answer key, so the key does not exist until people play.

| Game | You win by | Skill tested | Answer key |
| --- | --- | --- | --- |
| Feud-style games | Saying the most common answer | Thinking like the average person | Survey, fixed |
| Krillion | Saying the rarest valid answer | Breadth of obscure knowledge | Corpus frequency, fixed |
| Medallion | Saying the median answer | Reading what the crowd thinks is the middle | Live player responses, changes daily |

**Why it is a new skill, not a clone.** Both existing formats are one-directional: push toward obvious or push toward obscure. Medallion is a calibration target. You are not guessing the middle answer, you are guessing what everyone else will say, which makes it a Keynesian beauty contest closer to poker than to trivia.

**The reflexive loop (why the game cannot be solved).** If everyone aims for the same "middle" answer, that answer gets crowded and slides toward the common end, so the middle moves. Players reason in levels: level 0 says the first thought, level 1 skips the obvious, level 2 expects everyone to skip the obvious and goes one step further. The real median depends on each day's mix of levels.

This is the El Farol bar problem: any fixed strategy that works gets copied and stops working. There is no dominant strategy, so the meta keeps evolving as players get better. That is the game's longevity.

**The name.** Median, middle, medal. The winning answer each day is "the medal answer."

**Design principles**

1. **No account to play.** Anyone can open the site and play in about 4 minutes.
2. **The crowd is the answer key.** Live, player-relative scoring that cannot be scraped ahead of time.
3. **Luck hooks, skill retains.** The Daily 50 rewards a lucky day; the Overall 50 rewards consistency.
4. **The reveal is the product.** Seeing where you landed on the crowd curve is the moment that makes people come back.
5. **Low maintenance.** Built to run with a few hours of work a month. The goal is a cool project, not a revenue machine.

### Theme: weigh it, then mint it

Every answer is weighed on a brass balance scale and struck in metal; at the end of the day the 7 strikes become one minted coin. Krillion's theme is depth; Medallion's is balance and the coin.

**1. The scale (shown after every answer)**

1. A brass balance. Left pan is labelled "common," right pan "rare."
2. The beam tilts by the answer's signed distance from the med (section 3.2): too common tilts left, too rare tilts right, a Medallion answer sits perfectly level.
3. Tilt formula, capped at 20 degrees:

```latex
\text{tilt}^\circ = 20 \times \text{clamp}\left(\frac{d_{\text{signed}}}{0.5}, -1, 1\right)
```

4. d\_signed is negative when the answer's whole band sits below 0.5 (too common), positive when it sits above 0.5 (too rare), and 0 for a Medallion.
5. Animate the beam settling over about 1 second, then show the metal and score.

**2. Metal tiers** (rules in section 3.2)

| Tier | Meaning | Look |
| --- | --- | --- |
| Medallion | Your answer's band contains the med | Deep gold, engraved rim, a brief shine animation |
| Gold | Score 80 to 99 | Plain gold |
| Silver | Score 50 to 79 | Silver |
| Bronze | Score 20 to 49 | Bronze |
| Copper | Score 1 to 19 | Copper |
| No strike | Invalid or timed out, score 0 | Empty grey slot |

**3. The daily coin (end of day)**

1. A round coin with 7 notches around the rim, one per prompt, each coloured by that answer's metal.
2. The centre shows the day number and total score.
3. The coin's own metal comes from the average score (total ÷ 7) using the same thresholds: 80+ Gold, 50 to 79 Silver, 20 to 49 Bronze, below 20 Copper. All 7 answers Medallion = a Medallion coin.
4. The provisional coin shows on the end-of-run reveal with a "settles at midnight" stamp; the final coin is minted at settle.

**4. The vault (profile page)**

1. A grid of every coin the player has minted, newest first. Days not played show as an empty slot.
2. Streak = consecutive days with a minted coin.
3. For archive buyers, replayed days show as a lighter "replica" coin, so live and practice coins never mix.

**5. Words and look**

1. Verbs: you **strike** an answer, you **mint** your coin, the centre of the scale is **the med**.
2. Example lines: "Struck in Silver, 18 points from the med." "You minted a Gold coin. Top 4%."
3. Palette: brass, parchment and deep navy, with matching dark mode. Engraved-style serif for headings, clean sans-serif for body text.
4. Leaderboards keep their plain names (Daily 50, Overall 50); do not over-theme.

## 2. Daily game loop

One global puzzle drops at 12:00am Eastern; players see a provisional score the moment they answer, and everything locks into final scores, ranks and leaderboards at the next midnight.

**A day in the life of a puzzle**

1. **12:00am ET, Day 0.** The settle job locks yesterday and activates today's 7 prompts (pre-loaded from the content bank).
2. **Player opens the site.** If they played yesterday, the first thing they see is their settled result: final score, final percentile, and a Daily 50 callout if they made it.
3. **Seven prompts, one at a time.** 30 seconds per prompt. One answer per prompt. Timeout or invalid answer scores 0.
4. **Each answer is validated and scored instantly** against the current crowd estimate (sections 4 and 5). The player sees a short per-answer reveal: where their answer sits on the crowd curve and how far it was from the med.
5. **End-of-run reveal.** Total score, provisional percentile, a 7-row strip of distances, and the email opt-in prompt.
6. **All day:** more players answer, the crowd estimate sharpens, provisional scores move.
7. **12:00am ET, Day 1.** The crowd distribution freezes. Final scores, final percentiles, the Daily 50 and the rolling Overall 50 are computed. The day joins the archive.
8. **8:00am ET, Day 1.** Opted-in players get the results email. Settled results become shareable.

**Rules**

1. Same 7 prompts for every player, worldwide, for the full 24 hours.
2. One play per device per day (one per account once signed in). No retries.
3. The timer and scoring run on the server. The browser never receives answer lists or crowd data before settle.
4. The medal answer for each prompt stays hidden until settle, so early players cannot leak it to later ones.

| Field | Provisional (Day 0) | Final (Day 1) |
| --- | --- | --- |
| Per-answer score | Shown, can move | Locked |
| Total score | Shown, can move | Locked |
| Percentile | Estimated, normal model if few players | Exact empirical percentile once enough players |
| Medal answers | Hidden | Revealed with full distribution |
| Leaderboards | Not shown | Daily 50 and Overall 50 published |
| Shareable | No | Yes |

## 3. Scoring system

Each answer owns a band of the line from 0 (most common) to 1 (rarest), sized by its share of crowd responses, and scores up to 100 by how close its band sits to 0.5, the med. Each answer also earns a metal tier, and seven answers make a daily total out of 700.

### 3.1 Each answer owns a band on the crowd line

The middle is measured by **response mass**, not by list position. Answer lists are long-tailed (hundreds of rare answers), so the middle of the list would be obscure and Medallion would collapse into Krillion. The middle of what people actually say lands on "known but not first-thought" answers.

For one prompt:

1. Take every valid answer with its share s\_a of responses (blended live plus prior, section 4.2). Shares sum to 1.
2. Sort from most to least common. Break ties alphabetically by canonical text so the order is always the same.
3. Walk the sorted list. Each answer owns the band from lo to hi:

```latex
lo_a = \sum_{b \text{ before } a} s_b, \qquad hi_a = lo_a + s_a
```

4. The **medal answer** is the one whose band contains 0.5. If 0.5 lands exactly on a boundary, both neighbouring answers are medal answers.
5. A valid answer with no share yet (brand new, just accepted by the judge) sits at the rare end: lo = hi = 1.

### 3.2 Distance, score and metal tier

**Distance** from the med is zero if the band contains 0.5, otherwise the gap from the band's nearest edge to 0.5:

```latex
d_a = \begin{cases} 0 & lo_a \le 0.5 \le hi_a \\ \min(|lo_a - 0.5|,\ |hi_a - 0.5|) & \text{otherwise} \end{cases}
```

**Signed distance** (used by the scale animation): negative when hi < 0.5 (too common), positive when lo > 0.5 (too rare).

**Why bands and not a single point:** if everyone converges on one answer and it takes 60% of responses, its band covers 0 to 0.6, so it *is* the median and must score 100. Placing it at its midpoint (0.3) would wrongly score it about 41. Bands guarantee the medal answer and the 100-point answer are always the same thing.

**Score:**

```latex
\text{score}_a = 100 \cdot \exp\left(-\frac{d_a^2}{2\sigma^2}\right)
```

1. Round to a whole number.
2. If d > 0, cap at 99, so 100 always means Medallion.
3. Any valid answer scores at least 1, so a valid but very rare answer still earns Copper.
4. Invalid answers and timeouts score 0.
5. σ is stored per prompt. Default 0.15.

| Distance from the med | Score at σ = 0.15 | Tier |
| --- | --- | --- |
| 0 (band contains 0.5) | 100 | Medallion |
| 0.05 | 95 | Gold |
| 0.10 | 80 | Gold |
| 0.15 | 61 | Silver |
| 0.25 | 25 | Bronze |
| 0.35 | 7 | Copper |

**Metal tiers:** Medallion = distance 0. Gold = 80 to 99. Silver = 50 to 79. Bronze = 20 to 49. Copper = 1 to 19. No strike = 0.

The player also sees "18 points from the med," which is round(d × 100).

**Reference code (copy as the scoring function):**

```python
import math

def score_prompt(shares: dict[str, float], sigma: float = 0.15) -> dict:
    """shares: canonical answer -> blended share, summing to 1."""
    order = sorted(shares, key=lambda a: (-shares[a], a))
    results, lo = {}, 0.0
    for a in order:
        hi = lo + shares[a]
        if lo <= 0.5 <= hi:
            d, signed = 0.0, 0.0
        elif hi < 0.5:
            d = 0.5 - hi
            signed = -d   # too common
        else:
            d = lo - 0.5
            signed = d    # too rare
        results[a] = {"lo": lo, "hi": hi, "distance": d,
                      "signed": signed, **score_and_tier(d, sigma)}
        lo = hi
    return results

def score_and_tier(d: float, sigma: float) -> dict:
    raw = round(100 * math.exp(-(d ** 2) / (2 * sigma ** 2)))
    score = 100 if d == 0 else max(1, min(99, raw))
    if d == 0:
        tier = "medallion"
    elif score >= 80:
        tier = "gold"
    elif score >= 50:
        tier = "silver"
    elif score >= 20:
        tier = "bronze"
    else:
        tier = "copper"
    return {"score": score, "tier": tier}

# Invalid or timed-out answers never reach this code: score 0, tier "none".
# A valid answer missing from shares: lo = hi = 1, so d = 0.5.
```

**Prompt rule that falls out of the math:** if one answer holds more than half of all responses, its band always contains 0.5, so everyone who says the obvious answer gets 100 and Medallion becomes Family Feud. Only use prompts with flat distributions: top answer under 25% of simulated responses (section 9).

### 3.3 Daily total and tiebreaks

1. **Total** = sum of the 7 answer scores, 0 to 700.
2. **Tiebreak 1:** lower total distance from the med across all 7 answers wins.
3. **Tiebreak 2:** earlier final submission wins.

### 3.4 Final rank: the normal model

A daily total is a sum of 7 bounded scores, so across players it trends toward a normal distribution (Central Limit Theorem). Two caveats: totals are clipped at 0 and 700, and a skilled player scores well on all 7 (correlated), which widens the spread without breaking the shape much.

1. **Under 200 players that day:** fit a normal to the totals and report the smoothed percentile.

```latex
\text{percentile} = \Phi\left(\frac{x - \mu}{s}\right)
```

2. **200 players or more:** report the exact empirical percentile (share of players you beat). No assumptions needed.

### 3.5 Overall 50: rolling skill score

Each player's daily percentiles over the last 30 days are averaged, then shrunk toward 50 so a short lucky streak cannot top the board:

```latex
\text{adj} = \frac{n \cdot \overline{\text{pct}} + k \cdot 50}{n + k}, \quad k = 10
```

n = days played in the window. Minimum 10 days to qualify. At 10 days a player's average counts 50%; at 30 days it counts 75%. Raise the minimum to 15 or 20 later if the board fills with newcomers.

## 4. Cold start: scoring before the crowd exists

Every prompt starts with an AI-simulated crowd as its prior, and real player answers take over automatically as they arrive, so the game works on day one with 10 players and gets more accurate every day.

### 4.1 The prior: a simulated crowd

Corpus frequency measures what people *write*. Medallion measures what people *blurt* in 30 seconds. So the main prior is a simulated crowd, not a word-frequency table.

1. For each prompt, ask an LLM \~300 times: "You are a random adult. Name a pasta dish. Answer with the first thing that comes to mind."
2. Vary the persona per call (age, region, interests) and use high temperature, so the samples spread like real people instead of repeating one answer.
3. Canonicalize and count the answers. Their shares become the prior, q\_a.
4. Use corpus frequency (the `wordfreq` library) only as a sanity check and to give rare valid answers a small nonzero prior.

**Simulate strategic players, not only naive ones.** Real Medallion players are trying to hit the middle, so many skip their first thought (the reflexive loop, section 1). A simulation of only naive people would predict a median that is too common. Split the 300 samples:

1. **150 naive samples:** "You are a random adult. Name a pasta dish. Answer with the first thing that comes to mind."
2. **150 strategic samples:** "You are playing a game where you score points by naming the answer in the middle of what everyone else says. Most players skip the most obvious answer. Name a pasta dish."
3. Combine both into one count, then compute prior shares q\_a.
4. Store the naive/strategic split as a single setting (default 50/50). The calibration loop in 4.3 moves it toward whichever mix best predicts the real medal answers.

### 4.2 The blend: live data takes over smoothly

Treat the prior as k pseudo-players (a Dirichlet prior). With n real answers so far and c\_a of them saying answer a:

```latex
\hat{s}_a = \frac{c_a + k \cdot q_a}{n + k}, \quad k = 50
```

Positions and scores (section 3) are then computed from the blended shares.

| Real answers to a prompt (n) | Weight on real data |
| --- | --- |
| 10 | 17% |
| 50 | 50% |
| 200 | 80% |
| 1,000 | 95% |

No switch-over date is needed. The math fades the AI out on its own.

### 4.3 Calibrating the simulation

Every settle, compare the simulated crowd to the real one for each prompt: did the simulated medal answer match the real medal answer, and how far apart were the distributions? Track this as a daily metric. If the simulation proves accurate, keep k high; if it is biased, lower k so real data dominates sooner, and adjust the simulation prompt.

### 4.4 Honesty and seeding

1. Label the crowd in the UI: **"Crowd: estimated"** when a prompt has fewer than 100 real answers, **"Crowd: live"** after.
2. Early scores move the most. That is fine: it is exactly why scores settle at midnight, and it gives players a reason to come back.
3. Run a private beta of 30 to 50 people (friends, your existing networks) for 1 to 2 weeks before public launch. Their answers calibrate k, σ and the simulation, and they seed the first public days.

## 5. Answer validation

Answers are checked against a pre-built sheet first and sent to a cheap AI judge only on a miss, with every ruling cached, so the AI-generated sheets only need to be mostly right and fix themselves as people play.

### 5.1 The validation pipeline (runs on the server for every answer)

1. **Normalize:** lowercase, trim, strip punctuation and accents, collapse spaces, singularize simple plurals.
2. **Exact match** against the prompt's sheet, including aliases ("NYC" and "New York City" both map to one canonical answer).
3. **Fuzzy match:** allow 1 typo for answers of 5 to 8 letters, 2 typos for 9 or more. Never fuzzy-match short words, or "cat" becomes "car."
4. **Judge cache:** has this exact normalized text been ruled on before for this prompt? Use the stored ruling.
5. **AI judge** (only on a full miss): a small model returns valid or invalid, the canonical form, and a confidence from 0 to 1.
6. **Store the ruling.** Accepted answers join the sheet with their canonical form, so the next player who types it hits step 2.

**Why canonicalization matters:** live crowd counts are only correct if "Monopoly," "monopoly game" and "Monopolly" all count as one answer. Split counts silently corrupt the median. This is the most important unglamorous piece of the build.

### 5.2 Judge rules

1. Lenient by default: reject only when the judge is 0.8 or more confident the answer is nonsense or off-topic. Everything plausible is accepted. Rejections are logged for review and shown to the player as "Off the scale" (score 0), never as "rejected."
2. A judge-accepted answer counts for the player immediately but only joins the crowd distribution once a second, independent player gives it, or after admin review. This stops one person from inventing answers that bend the median.
3. Offensive answers are accepted and scored normally, but hidden from every public display: results charts fold them into the Other bucket. Handles are still filtered, since they are display only.

### 5.3 Abuse controls

1. One answer per prompt per device per day, so each device can trigger at most 7 judge calls a day.
2. Rate-limit by IP to block one person scripting many devices.
3. Add an invisible bot check (for example Cloudflare Turnstile) on the play endpoint if abuse appears.
4. Leaderboard eligibility requires a verified email (section 7).

**Coordination is part of the game.** Groups agreeing to all enter the same *valid* answer is the beauty contest played at group scale, so it is allowed. Small raids usually overshoot: the raided answer piles up mass and slides toward the common end, so raiders lose. Only a raid larger than half of all players can capture a day, which is only possible while the player base is small, and that attention is free marketing. Nonsense raids are blocked by the off-topic rule above.

### 5.4 Admin review queue

A simple private page listing each day's judge rulings, low-confidence rejections and the most common rejected answers. Spend 10 minutes a day there for the first month: override bad rulings, add missing aliases. Overrides apply before settle.

## 6. Reveal screen and share card

The reveal shows players where they landed on the crowd curve without exposing the medal answer until settle, and the share card goes out only with final results the next day.

### 6.1 Per-answer reveal (after each prompt, about 3 seconds)

1. **The scale:** a brass balance with a "common" pan on the left and a "rare" pan on the right. The beam tilts by your signed distance and settles level for a Medallion (theme, section 1).
2. **Your strike**: the metal stamp (Medallion, Gold, Silver, Bronze, Copper) and the line "8 points from the med."
3. **Your score** for that answer, out of 100.
4. **Not shown:** the medal answer and other players' answers. Revealing the medal answer during the day would let early players post it and collapse the game for everyone after them.

### 6.2 End-of-run reveal

1. Provisional total out of 700 and provisional percentile, with a "settles at midnight" note.
2. The provisional coin: 7 rim notches coloured by metal, stamped "settles at midnight."
3. Crowd label per prompt: "estimated" or "live" (section 4.4).
4. Email opt-in: "Your rank settles at midnight. Want it in your inbox?"

### 6.3 Settled view (next visit, Day 1)

1. The minted coin, final total, final percentile and world rank.
2. If in the Daily 50: a large callout, "You finished #37 in the world yesterday."
3. Per prompt: the full crowd distribution chart, your answer marked, and the medal answer revealed.
4. One highlighted crowd insight: "Most people thought the middle pasta was penne. It was rigatoni."
5. Share button, then "Today's 7 are live."

### 6.4 Share card (final results only)

The share card is the minted coin as plain text. It never contains answers, so it cannot spoil anything, and it is only available after settle.

```markdown
Medallion #12 · Gold coin · 612/700 · top 4%
M G G S M S B
#37 in the world
playmedallion.com
```

1. **Line 1:** day number, coin metal, total, percentile.
2. **Line 2:** one letter per prompt in order. M = Medallion, G = Gold, S = Silver, B = Bronze, C = Copper, X = no strike. (The build may swap letters for coloured squares later.)
3. **Line 3:** only if the player made the Daily 50. Otherwise omitted.
4. **Line 4:** the site link.
5. The share button copies this text and, on phones, opens the native share sheet.

Because sharing happens the morning after, it pairs with the results email: the email delivers the coin, the share button sits right under it.

## 7. Identity, email and leaderboards

Playing needs no account; a verified email is optional and unlocks the morning results email, cross-device history and leaderboard eligibility.

### 7.1 Anonymous by default

1. On first visit, the server issues a random player ID, stored in the browser (cookie plus local storage).
2. Every submission is tied to that ID. Results, streaks and history live on the server under it.
3. Weakness: clearing the browser or switching devices loses the history. The email signup fixes this.

### 7.2 Email signup (optional)

1. Offered at the end-of-run reveal, the moment curiosity peaks.
2. **The pitch:** "Get your final rank by email and keep your streak on every device."
3. **Magic link login:** the player types an email, clicks the link in their inbox, done. No password.
4. On verification, the anonymous history merges into the account.
5. At signup the player picks a public handle (filtered for offensive words) and the browser's time zone is saved.

### 7.3 The morning results email

1. **When:** 8:00am ET for everyone, the morning after they played. One send per day.
2. **Subject line:** the result, e.g. "You finished top 4% yesterday" or "You finished #37 in the world."
3. **Body:** final score and percentile, Daily 50 placement if any, one crowd insight, a share button, and "Today's 7 are live."
4. **Only sent if they played** that day. No play, no email, so it never feels like spam.
5. **Compliance (CAN-SPAM):** one-click unsubscribe and a physical mailing address in every email.
6. **List hygiene:** stop emailing anyone who has not opened in 30 days. Protects deliverability.

### 7.4 Leaderboards

| Board | Ranks by | Eligibility | Resets | Purpose |
| --- | --- | --- | --- | --- |
| Daily 50 | That day's final total (tiebreaks in 3.3) | Verified email | Every midnight | The hook: an average player on a lucky day can make it |
| Overall 50 | Rolling 30-day shrunk average percentile (3.5) | Verified email, 10 or more days played in the window | Rolling, recalculated nightly | The prestige board: rewards consistent skill |

**Amplify the lucky moment.** When a first-timer makes the Daily 50, make it loud: the settled-view callout, the email subject line, a share card with the placement, and a nudge: "Play 10 days to qualify for the Overall 50." That turns one lucky day into a habit.

**Why boards require a verified email:** it blocks throwaway-device farming and gives serious players a concrete reason to sign up. The funnel becomes: play anonymously, see your rank, want the board, sign up.

## 8. Monetization: the unlimited archive

The daily game stays free forever; the one paid feature is unlimited replay of past days, priced as a one-time unlock of about $4.99 and switched on once the archive has around 30 days in it.

### 8.1 Free vs paid

| Feature | Free | Archive unlock |
| --- | --- | --- |
| Today's 7 prompts | Yes | Yes |
| Yesterday's settled results and medal answers | Yes | Yes |
| Leaderboards and morning email | Yes (with email) | Yes |
| Replay any past day | No | Yes, unlimited |

### 8.2 How archive replays work

1. A replay is scored against that day's **frozen** crowd distribution (saved at settle), so the core mechanic stays true.
2. Replays never change the frozen crowd, the leaderboards or the player's daily stats. They are practice and fun, nothing more.
3. The archive needs past days, so it is empty at launch. Turn the paywall on once roughly 30 days exist.

### 8.3 Pricing logic

Stripe takes a percentage plus a fixed 30 cents per charge, and the fixed part punishes small prices.

| Price | Stripe fee (2.9% + $0.30) | Fee as share of price | You keep |
| --- | --- | --- | --- |
| $1.99 | $0.36 | 18% | $1.63 |
| $2.99 | $0.39 | 13% | $2.60 |
| $4.99 | $0.44 | 9% | $4.55 |

Recommended: **$4.99 one-time.** A subscription adds churn handling, renewal emails and cancellations for very little extra money at this scale.

### 8.4 Purchase flow

1. Buying requires a verified email (the unlock must survive device changes).
2. Stripe Checkout handles the payment page; no card data ever touches your server.
3. A Stripe webhook marks the account as unlocked.
4. Turning on payments makes the project commercial, which changes your hosting plan (section 11).

## 9. Content pipeline: 100 days in one day

AI generates and filters about 1,000 candidate prompts, you hand-pick 700 (100 days × 7) in a few hours, and AI builds the answer sheets; runtime validation (section 5) covers whatever the sheets miss.

### 9.1 What makes a good Medallion prompt

1. **Flat distribution:** the top answer gets under 25% of simulated responses. If one answer dominates, it becomes the median and the game turns into Family Feud.
2. **Wide pool:** at least 50 valid answers, so there is a real middle.
3. **Clear boundary:** anyone can tell valid from invalid. "Name a strategy board game" works; "Name something you ride standing up" invites disputes.
4. **Timeless:** no answers that change with the news or the season (unless scheduled for that date on purpose).
5. **Universal:** answerable by an adult anywhere, not just one country or one hobby.
6. **Prefer enumerable sets** (countries, board games, dog breeds, cheeses) where a reference list exists, because validation becomes a lookup.

### 9.2 The pipeline

1. **Generate candidates:** about 1,000 prompts across 10 to 12 categories (food, geography, pop culture, sports, nature, language, history, everyday objects, science, entertainment, wildcard).
2. **Simulate the crowd** for every candidate: 300 persona-varied samples (section 4.1).
3. **Auto-filter** using the simulation: drop prompts whose top answer exceeds 25% share or that produce fewer than 30 distinct answers.
4. **Human cut (you, 2 to 3 hours):** pick the best 700. Kill anything ambiguous, boring or near-duplicate. This is the only step that needs your judgment.
5. **Build answer sheets:** 3 independent generation passes per prompt, take the union, add common aliases and misspellings.
6. **Verifier pass:** a separate model call strips invalid answers from each sheet.
7. **Attach priors:** simulated shares from step 2, corpus frequency as a floor for rare answers.
8. **Spot-check about 5%** of sheets (35 prompts). If the error rate is low, ship.
9. **Schedule:** assign prompts to dates. Each day gets a mix of categories and difficulty, no two prompts from one category on the same day.

### 9.3 Stored format per prompt

```json
{
  "date": "2026-12-01",
  "slot": 3,
  "prompt": "Name a pasta dish",
  "category": "food",
  "sigma": 0.15,
  "answers": [
    {"canonical": "rigatoni alla vodka", "aliases": ["vodka rigatoni"], "prior_share": 0.031},
    {"canonical": "carbonara", "aliases": ["spaghetti carbonara"], "prior_share": 0.142}
  ]
}
```

### 9.4 Keeping it running

1. Bank 100 days before launch. That is over 3 months of runway.
2. Refill monthly in one sitting, same pipeline, about half a day.
3. Feed the rejected-answer log and judge rulings back into future sheets.
4. Use real crowd data to improve the simulation prompt over time (section 4.3).

## 10. Tech stack, architecture and data model

A Next.js app on Vercel talks to a Supabase Postgres database; all scoring happens on the server, two scheduled jobs run the day (midnight settle, 8:00am ET email), and Resend, Stripe and an LLM API sit at the edges.

&#91;embedded content: system architecture · 7 components\]

The browser only sends answers and receives scores; answer sheets, crowd counts and medal answers stay in the database until the midnight settle.

### 10.1 Stack

| Layer | Choice | Job |
| --- | --- | --- |
| Frontend and API | Next.js on Vercel | Game UI, play and reveal endpoints, admin page |
| Database and auth | Supabase (Postgres + magic link auth) | All game data, accounts, crowd counts |
| Scheduled jobs | Supabase pg\_cron or scheduled functions | Midnight settle, 8:00am ET email sender |
| Email | Resend | Morning results email, magic links |
| Payments | Stripe Checkout + webhook | Archive unlock |
| AI | An LLM API | Content generation, crowd simulation, runtime judge |
| Bot protection (if needed) | Cloudflare Turnstile | Invisible check on the play endpoint |

### 10.2 Data model

| Table | Key fields | Notes |
| --- | --- | --- |
| prompts | id, date, slot (1 to 7), text, category, sigma | Pre-loaded from the content bank |
| answers | id, prompt\_id, canonical, aliases, prior\_share, source (sheet, judge, admin) | The answer sheet, grows over time |
| players | id, device\_id, email, handle, time\_zone, archive\_unlocked | Email and handle empty until signup |
| submissions | id, player\_id, prompt\_id, raw\_text, answer\_id, valid, served\_at, submitted\_at | One per player per prompt per day |
| crowd\_snapshots | prompt\_id, answer\_id, final\_share, final\_position | Frozen at settle, used by archive replays |
| daily\_results | player\_id, date, total, distance\_sum, percentile, rank | Written at settle |
| judge\_cache | prompt\_id, normalized\_text, verdict, canonical, confidence | Every AI ruling, reused |
| replays | player\_id, date, total | Archive plays, never touch live data |

### 10.3 Scheduled jobs

1. **Settle (12:00am ET daily):** freeze each prompt's crowd into crowd\_snapshots, compute final scores, percentiles (normal or empirical, section 3.4), ranks, the Daily 50 and the rolling Overall 50; then activate the new day's prompts.
2. **Email sender (8:00am ET daily):** send results to every opted-in player who played yesterday.
3. **Provisional scores:** computed on request from live counts plus the prior, cached for about a minute. No job needed.

### 10.4 Server-side rules (non-negotiable)

1. The browser never receives answer sheets, crowd counts or medal answers before settle. Otherwise anyone can open developer tools and read the key.
2. The timer is enforced on the server: each prompt records when it was served, and answers arriving more than 30 seconds plus a 2-second grace period later score 0.
3. Scores are computed only on the server. The client only displays them.
4. Database row-level security on: players can read only their own submissions and results.

## 11. Costs and launch budget

Launch costs only the domain; the first real bill is $20 a month for Vercel Pro on the day payments turn on, and the next two ($25 Supabase, $20 Resend) arrive only with real traction.

| Service | Free tier | Free tier limit that matters | You pay when | Paid price | Source |
| --- | --- | --- | --- | --- | --- |
| Domain (GoDaddy) | None | n/a | Day 1 | Check at checkout; renewal is usually higher than year one; decline all add-ons | n/a |
| Vercel (hosting) | Hobby, $0 | Non-commercial use only | Paywall turns on (commercial) | Pro, $20 per user per month | [source](https://zplatform.ai/guides/is-vercel-free/) |
| Supabase (database) | Free, $0 | 500 MB database, 50,000 monthly active users; pauses after 7 days idle (a daily game never idles) | You want daily backups or outgrow the caps | Pro, $25 per month | [source](https://dev.to/mahdi_benrhouma_fe1c6005/supabase-free-tier-limits-in-2026-what-you-actually-get-and-when-you-will-hit-them-4mhe) |
| Resend (email) | Free, $0 | 3,000 emails a month, 100 a day: about 100 daily subscribers | Over \~100 email subscribers | Pro, $20 per month for 50,000 emails | [source](https://www.usecarly.com/blog/resend-pricing/) |
| Stripe (payments) | No monthly fee | n/a | Per sale | 2.9% + $0.30 per US card charge | [source](https://www.size.ly/tools/stripe-fee-calculator) |
| LLM API | Pay per use | n/a | Content generation and judge calls | Approximate: a few dollars a month at this scale | Estimate |

**Budget by stage**

1. **Build and private beta:** $0 (Vercel Hobby, free tiers, a few dollars of LLM usage to generate content).
2. **Public launch:** the domain only, plus LLM usage.
3. **Paywall on (around day 30):** + $20 per month.
4. **Real traction (100+ email subscribers, backups wanted):** + $20 Resend, + $25 Supabase. Around $65 per month total, which 15 archive sales a month at $4.99 would cover.

Prices as of November 2026; recheck each provider's pricing page before paying.

## 12. Build phases, metrics, risks and locked decisions

The plan runs in six phases over about five weeks to public launch, with the paywall around day 30 after launch; each phase has a gate that must pass before the next starts.

### 12.1 Phases

1. **Phase 0: Content bank (week 1).** Run the pipeline in section 9. Gate: 700 prompts scheduled, sheets spot-checked, simulated priors attached.
2. **Phase 1: Core game (weeks 1 to 2).** Play loop, server timer, validation pipeline with judge and cache, scoring with the blended prior, per-answer and end-of-run reveals, anonymous IDs. Gate: you can play a full day end to end on a test date.
3. **Phase 2: Settle and results (week 2).** Midnight settle job, crowd snapshots, final percentiles, settled view with medal answers, admin review page. Gate: three simulated days settle correctly with fake players, including ties and timeouts.
4. **Phase 3: Private beta (weeks 3 to 4).** 30 to 50 testers for 10 to 14 days. Tune σ, k, the judge threshold and the timer. Gate: no scoring bugs for 5 straight days, testers come back without being reminded.
5. **Phase 4: Public launch (week 5).** Domain, email signup with magic link, morning email, share card, Daily 50, privacy policy and terms. Gate: launch.
6. **Phase 5: Retention and money (launch + 10 to 30 days).** Overall 50 goes live once 10 days of data exist; archive paywall, Stripe and Vercel Pro once about 30 days are archived.

### 12.2 Metrics to watch

| Metric | What it tells you | Healthy early sign |
| --- | --- | --- |
| Next-day return rate | Is the hook working | 25% or more of players return the next day |
| 7-day return rate | Is it a habit | Steady or rising week over week |
| Completion rate | Are prompts and timer fair | 90% or more finish all 7 |
| Email opt-in rate | Is the reveal pitch working | 10% or more of finishers |
| Share rate | Is growth possible | Any steady share activity from settled results |
| Judge disputes | Are answer sheets good enough | Rejected-valid answers falling each week |
| Simulation accuracy | Can the AI prior be trusted | Simulated medal answer close to the real one most days |
| Archive conversion | Will the paywall pay for hosting | About 15 sales a month covers costs |

The thresholds are starting guesses, not benchmarks; adjust them after the beta.

### 12.3 Risks

| Risk | Impact | Mitigation |
| --- | --- | --- |
| Too few players, so the crowd stays mostly AI | Game feels arbitrary | Strong simulated prior, honest "estimated" label, private beta seeding, share card growth |
| Valid answers rejected | Players feel cheated and quit | Runtime AI judge, judge cache, daily admin review |
| Medal answers leak during the day | Game collapses for later players | Never reveal medal answers or crowd data before settle |
| Groups coordinate on one answer | Nonsense raids could bend the median; valid raids can capture a day while the player base is small | Nonsense is filtered as off-topic; valid raids are allowed as part of the game; one play per device and a verified email for boards |
| Prompts with one dominant answer | Medallion turns into Feud | Auto-filter: top share under 25% in simulation |
| Content runs out | No game | 100-day bank, monthly refill |
| Name conflict with "Medallion" | Forced rename later | Quick USPTO trademark search before launch (not legal advice) |

### 12.4 Locked decisions

Every decision is locked so Claude Code never has to guess. Change a value here, not in code.

| Decision | Locked value |
| --- | --- |
| Puzzle reset | 12:00am America/New\_York, same prompts worldwide |
| Timer per prompt | 30 seconds, plus a 2-second server grace period |
| Default σ | 0.15, stored per prompt |
| Prior weight k | 50 pseudo-answers |
| Simulation split | 50% naive, 50% strategic, 300 samples per prompt |
| Judge acceptance threshold | Lenient: reject only when 0.8 or more confident the answer is nonsense or off-topic |
| Percentile method | Normal fit under 200 players that day, empirical at 200 or more |
| Overall 50 | 30-day window, minimum 10 days played, shrinkage k = 10 |
| Medal answer visibility | Hidden until settle |
| Archive price | $4.99 one-time |
| Streaks | Yes: shown in the vault and in the results email |
| Results email time | 8:00am ET, one send for everyone |
| Handles | 3 to 20 characters, letters, numbers and underscores, unique, checked against an open-source profanity word list |
| Domain | playmedallion.com; fallbacks in order: medallion.game, medallion.gg |
| Stack | Next.js App Router, TypeScript, Tailwind, Supabase, Resend, Stripe, Anthropic API |
| AI model | The smallest current Claude model (Haiku tier) for the judge and simulation |
| Trademark | USPTO name search before buying the domain (Phase 4 task) |
| Privacy policy and terms | Generated from a standard template before launch (Phase 4 task) |
| Offensive answers | Accepted and scored; hidden from public charts (folded into Other) |
| Coordinated raids | Allowed when the answer is valid; nonsense raids filtered as off-topic |

## 13. Build instructions for Claude Code

Build one phase at a time from this spec; the endpoints, screens and tests below are the contract between frontend and backend, and nothing outside them should be invented.

### 13.1 Global rules

1. **Game day** = the calendar date in America/New\_York. Store every timestamp in UTC.
2. **Player identity:** an httpOnly cookie `mdl_pid` issued by the server on the first request. Signed-in players also carry a Supabase session; the server links the two.
3. **Secrecy:** no endpoint ever returns answer sheets, shares, counts or medal answers for an unsettled day. Prompt text is returned only by the start endpoint, so nobody can pre-read all 7.
4. **Scoring:** port the reference function in section 3.2 to TypeScript exactly. Server only.
5. **Errors:** JSON `{ "error": "<code>", "message": "<text>" }` with the HTTP status listed.

### 13.2 API endpoints

| Endpoint | Purpose | Input | Output |
| --- | --- | --- | --- |
| GET /api/today | Today's game state | none | date, dayNumber, slots 1 to 7 each with status (not\_started, in\_progress, answered), yesterdaySettled summary if the player played yesterday |
| POST /api/prompt/start | Start one prompt; starts the server timer | slot | slot, text, deadline (ISO); 409 if already started |
| POST /api/answer | Submit an answer | slot, text (empty string on timeout) | valid, canonical, score, tier, distance, signed, crowdLabel (estimated or live); never the medal answer; 409 if already answered; late answers return score 0, tier none, reason timeout |
| GET /api/run | End-of-run summary | none | provisional total, provisional percentile, per slot score, tier and distance, provisional coin metal |
| GET /api/results/:date | Settled view for one day | date | final total, percentile, rank, coin, per prompt: your answer, score, tier, medalAnswers, distribution (top 30 answers with share, lo, hi, plus an Other bucket), daily50Rank if any; 425 if the day is not settled |
| GET /api/leaderboard | Daily or Overall 50 | board (daily or overall), date for daily | up to 50 rows: rank, handle, total or adjusted percentile |
| GET /api/vault | The player's coins | none | coins newest first: date, metal, total, 7 notch tiers, replica flag; current streak |
| POST /api/auth/magic-link | Send sign-in link | email | 200 always (no email enumeration) |
| POST /api/account/merge | Attach device history to the account after sign-in | none (uses cookie + session) | merged day count |
| POST /api/account/profile | Set handle and time zone | handle, timeZone | profile; 422 if handle invalid or taken |
| POST /api/email/unsubscribe | One-click unsubscribe | token | 200 |
| POST /api/checkout | Start archive purchase | none (signed in) | Stripe Checkout url; 401 if not signed in |
| POST /api/stripe/webhook | Mark archive unlocked | Stripe event (signature verified) | 200; 400 on bad signature |
| POST /api/replay/:date/start | Start a replay prompt | slot | slot, text, deadline; 402 if archive locked |
| POST /api/replay/:date/answer | Score a replay answer against the frozen snapshot | slot, text | same shape as /api/answer, plus the medal answer (the day is settled) |
| GET /api/admin/review | Judge rulings and rejections for a day | date | rulings list; 403 unless email is in ADMIN\_EMAILS |
| POST /api/admin/ruling | Override a ruling or add an alias | promptId, normalizedText, verdict, canonical | updated ruling; 403 unless admin |
| POST /api/jobs/settle | Midnight settle (section 10.3) | header x-cron-secret | settle report; 401 without secret |
| POST /api/jobs/send-emails | Daily email run at 8:00am ET | header x-cron-secret | sent count; 401 without secret |

### 13.3 Screens

| Route | Screen | What it shows |
| --- | --- | --- |
| / | Play | Yesterday's settled banner (if played), then a Start button, the 7 prompts one at a time with a 30-second timer, the scale reveal after each, then the end-of-run reveal with provisional coin and email opt-in. If already played today: the end-of-run reveal. |
| /results/\[date\] | Settled view | Minted coin, final total, percentile, rank, Daily 50 callout, per prompt distribution chart with your answer and the medal answer, one crowd insight, share button |
| /vault | Vault | Grid of minted coins newest first, empty slots for missed days, streak counter |
| /leaderboard | Leaderboards | Two tabs: Daily 50 (date picker) and Overall 50 |
| /signin | Sign in | Email field, magic link sent message |
| /auth/callback | Sign-in landing | Completes sign-in, calls merge, then asks for handle if missing |
| /settings | Settings | Handle, email on or off, time zone, sign out |
| /archive | Archive | Calendar of past days; locked days show the $4.99 unlock button |
| /archive/\[date\] | Replay | Same play flow as /, scored against the frozen snapshot, replica coin at the end |
| /how-to-play | How to play | The scale, the metals, the settle at midnight, in under 150 words plus one example |
| /privacy and /terms | Legal | Template text |
| /admin | Admin review | Today's judge rulings, low-confidence rejections, most common rejected answers, override buttons |

### 13.4 Acceptance tests

Each phase is done only when its tests pass. Write them as automated tests before building the feature.

**Scoring (Phase 1)**

1. Shares {A: 0.6, B: 0.3, C: 0.1}: A scores 100 Medallion; B scores 80 Gold (band 0.6 to 0.9, distance 0.1); C scores 3 Copper (distance 0.4).
2. Shares {A: 0.3, B: 0.3, C: 0.4}: order is C, A, B. C scores 80 Gold; A scores 100 Medallion; B scores 41 Bronze.
3. Shares {A: 0.5, B: 0.5}: both score 100 Medallion (0.5 sits on the boundary).
4. A valid answer missing from shares scores 1 Copper.
5. Any distance above 0 never scores 100; distance 0 always scores 100.
6. Blend with k = 50: with no real answers, blended shares equal the prior; with 50 real answers all for A and a prior share of 0 for A, A's blended share is 0.5.
7. Scale tilt: signed distance -0.25 gives -10 degrees; +0.6 gives +20 degrees (clamped).

**Validation (Phase 1)**

8. "Monopoly", "monopoly " and "MONOPOLY!" resolve to the same canonical answer.
9. "Monopolly" fuzzy-matches "Monopoly."
10. "cat" does not fuzzy-match "car" (short words never fuzzy-match).
11. An unknown answer calls the judge once; the same answer from a second player hits the cache and does not call the judge.
12. A judge-accepted new answer scores for its player but does not enter the crowd distribution until a second player gives it or an admin approves it.

**Timer and secrecy (Phase 1)**

13. An answer 31.5 seconds after start is accepted; one at 32.1 seconds scores 0 with reason timeout.
14. Answering the same slot twice from one device returns 409.
15. GET /api/today never contains prompt text; text appears only in the start response.
16. No response for today's date contains any other answer, any share, any count or the medal answer (scan every JSON response in the test run).

**Settle (Phase 2)**

17. After settle, crowd\_snapshots exist for all 7 prompts, and final scores match scores recomputed from the snapshot.
18. Slots never answered are recorded as 0 at settle.
19. With 50 players the percentile uses the normal fit; with 250 it uses the empirical percentile.
20. Equal totals are ordered by lower distance sum, then earlier final submission.
21. Replays after settle never change snapshots, leaderboards or daily results.

**Accounts and leaderboards (Phase 4 and 5)**

22. Players without a verified email never appear on either board.
23. Overall 50: a player with 9 days in the window is excluded; a player with 10 days averaging the 90th percentile has an adjusted score of 70.
24. Merge: a device's 5 played days appear in the account's vault after sign-in.

**Email (Phase 4)**

25. Every opted-in player who played gets exactly one email in the 8:00am ET run. A player who did not play, or who unsubscribed, gets none. An offensive answer never appears in any public results response.

**Payments (Phase 5)**

26. A webhook with a valid signature unlocks the archive; an invalid signature returns 400 and changes nothing.
27. A locked player gets 402 from the replay endpoints.

### 13.5 Environment variables

1. `NEXT_PUBLIC_SUPABASE_URL`, `NEXT_PUBLIC_SUPABASE_ANON_KEY` (safe for the browser)
2. `SUPABASE_SERVICE_ROLE_KEY` (server only)
3. `ANTHROPIC_API_KEY` (server only)
4. `RESEND_API_KEY` (server only)
5. `STRIPE_SECRET_KEY`, `STRIPE_WEBHOOK_SECRET`, `STRIPE_PRICE_ID` (server only)
6. `CRON_SECRET` (server only, sent by the scheduler)
7. `ADMIN_EMAILS` (comma-separated)
8. `SITE_URL`

Only variables starting with `NEXT_PUBLIC_` may ever reach the browser.

### 13.6 CLAUDE.md (save in the repo root)

```markdown
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
```

### 13.7 How to run each Claude Code session

1. Export this doc as Markdown and save it as `SPEC.md` in the repo root, next to `CLAUDE.md`.
2. Start each session with: "Read CLAUDE.md and SPEC.md. Build Phase N from section 12.1. Write the matching acceptance tests from 13.4 first, make them pass, then stop and summarize what you built."
3. After each phase, play it yourself for a few minutes before starting the next one.
4. When you change a decision, edit section 12.4 here, re-export SPEC.md, and tell Claude Code what changed.
