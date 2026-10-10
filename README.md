# Dynamic Programming: How to Spot DP Problems, Build the Recurrence, and Practice the Patterns Interviews Actually Test

Most people don't fail dynamic programming because they can't write a loop. They fail at minute twelve of a forty-five-minute interview, still not sure whether the problem in front of them is DP, greedy, or plain recursion — and by the time they commit, there's no room left to code it.

So the real difficulty isn't the code. It's the recognition step, and then the state definition. Everything after that is mechanical if you got those two right.

This is a working guide to both, plus where a pattern-based platform like AlgoMonster fits into the picture — including what it covers well and where it's thin.

## What dynamic programming actually means

A problem is a DP problem when two things are true at once:

- **Overlapping subproblems.** You solve the same smaller case more than once. Naive Fibonacci is the textbook example: `fib(5)` and `fib(4)` both end up computing `fib(3)`.
- **Optimal substructure.** The best answer for the whole problem is built from the best answers to its parts. If the optimal solution to the full problem can't be assembled from optimal sub-solutions, DP gives you the wrong answer.

Miss either condition and you're doing extra work for nothing. Climb a staircase with `n = 50` and naive recursion gets you roughly 2^50 calls. Memoize it and you're at 50.

That's the whole trade: memory in exchange for not doing the same work twice.

## The five-question routine that makes DP tractable

Almost every DP solution falls out of the same sequence. Instead of asking "what's the DP formula for this," ask:

1. **What does `dp[i]` (or `dp[i][j]`) mean in plain English?** Not "the DP value" — something like "the minimum number of coins needed to make amount `i`."
2. **How can I reach the current state?** For `dp[i]`, what came immediately before it? For `dp[r][c]`, which cells feed into this one?
3. **Write the recurrence** from that answer.
4. **Find the base cases.** Empty string, zero amount, first row and column.
5. **Fill the table in an order where dependencies are already computed.**

Step one is where interviews are won and lost. If your state definition is vague, the recurrence will be wrong and you'll spend fifteen minutes debugging a formula that was never well-defined. If your state definition is precise, the recurrence is often a two-line translation of step two.

A useful sanity check before you type: say the state out loud as a sentence. "dp[i] is the maximum money I can take from the first i houses." If that sentence is awkward, your state is wrong.

## The DP patterns that keep showing up

DP isn't one technique — it's a family of about a dozen recurring shapes. Recognizing which shape you're in is most of the battle. These are the ones that appear constantly in interviews:

| Pattern | Trigger words in the problem | State | Representative problems |
| --- | --- | --- | --- |
| Linear / decision DP | "number of ways", "climb", "distinct ways" | `dp[i]` = answer for first i items | Climbing Stairs, Fibonacci, Decode Ways |
| Min / max to target | "minimum", "fewest", "cheapest" | `dp[i]` = best cost to reach i | Coin Change, Minimum Path Sum, Minimum Cost Climbing Stairs |
| Take / skip | "maximum profit", "rob", "can't pick adjacent" | `dp[i]` = best using first i, item i taken or skipped | House Robber I & II, stock DP variants |
| Grid DP | "grid", "matrix", "robot", "paths" | `dp[r][c]` = answer for cell (r,c) | Unique Paths, Unique Paths II, Minimum Path Sum |
| 0/1 Knapsack | "subset", "partition", "target sum", "can we form X" | `dp[i][cap]` = best using first i items within capacity | Partition Equal Subset Sum, Target Sum, Ones and Zeroes |
| Unbounded knapsack | items reusable, order may or may not matter | same shape, forward iteration | Coin Change, Combination Sum IV, Word Break |
| String / subsequence DP | "two strings", "subsequence", "edit distance" | `dp[i][j]` = answer for prefixes of both strings | LCS, Edit Distance, Longest Palindromic Subsequence |
| Longest Increasing Subsequence | "longest increasing", "chain", "envelope" | `dp[i]` = best subsequence ending at i | LIS (O(n²) and the O(n log n) binary-search version) |
| Kadane / max subarray | "contiguous subarray", "max sum" | `dp[i]` = best subarray ending at i | Maximum Subarray, Maximum Product Subarray |
| Interval DP | "merge", "burst", "range" | `dp[i][j]` = answer for the range [i, j] | Burst Balloons, Matrix Chain Multiplication |
| State machine DP | allowed transitions between named states | `dp[i][state]` | Best Time to Buy and Sell Stock with cooldown |
| Tree DP | "tree", "subtree", "include or exclude node" | returns per node | House Robber III, Binary Tree Maximum Path Sum |
| Bitmask DP | small set (≤ 20 elements), "visit each exactly once" | `dp[mask][i]` | Traveling Salesman Problem, assignment problems |

A pattern worth flagging because it's underrated: **Kadane's algorithm**. It looks like a one-off trick for Maximum Subarray, but it's really DP where the decision at each index is "extend the current run or restart here." Once you see it that way, Maximum Product Subarray (track both max and min, because a negative flips them), Maximum Sum Circular Subarray, and single-transaction stock problems stop looking like separate problems.

## Top-down or bottom-up? Pick based on the interview, not your preference

**Memoization (top-down)** is recursion plus a cache. You write the recurrence exactly as you'd say it out loud, then add a lookup at the top of the function.

- Easier to derive, because it mirrors the recurrence directly.
- Only computes the states you actually need, which matters when the state space is sparse.
- Costs you a recursion stack. At `n` in the hundreds of thousands, or on a graph-shaped state space, you can blow the stack before you run out of memory.

**Tabulation (bottom-up)** is an array you fill in dependency order.

- No recursion depth limits.
- Straightforward to optimize: if `dp[i]` only depends on `dp[i-1]` and `dp[i-2]`, you don't need the array at all — two variables is enough.
- Harder to derive, because you have to think about iteration order up front.

The practical advice: start top-down to get the recurrence right, then convert if the constraints demand it. Interviewers generally accept either. What they push on is whether you can explain *why* your state is correct, and what happens to complexity when the input grows.

## The follow-up question that catches people

Once you've written a correct DP, the interviewer's next move is almost always space.

- `O(n)` array → two rolling variables, `O(1)` space, when each state depends on a fixed small window.
- 2D table → two rows, `O(n)` space, when each row depends only on the previous row.
- 0/1 knapsack specifically → iterate capacity **high to low** in the 1D version, otherwise you'll reuse the same item twice and silently compute the unbounded answer.

Knowing when *not* to bother matters too. If the input is bounded at a few thousand elements, the extra complexity of a rolling array buys you nothing and adds a bug surface.

## Why this is a recognition problem more than a coding problem

Ask anyone who's prepared seriously and they'll describe the same failure mode: the code went fine, the approach didn't. Fifteen of your forty-five minutes disappear exploring a greedy solution that doesn't hold, or an approach that isn't DP at all.

Greedy fails on the classic Coin Change because taking the biggest coin first can block the optimal combination. Greedy works on Activity Selection because the greedy choice property happens to hold there. There's no shortcut to knowing which is which beyond having seen the shapes and internalized the tells.

That's what pattern-based prep is for. It isn't about solving more problems — it's about attaching each problem to a named pattern with a reusable template, so that next time you see "minimum number of ..." you're already halfway to the state definition.

## Where AlgoMonster fits into a DP study plan

AlgoMonster is an interview prep platform built by ex-Google engineers (the curriculum credit line on their site names Google engineers and competitive programmers). It's not a problem bank. The structure is a course: roughly 48 patterns, each with a lesson, a code template, and practice problems tied to it.

For dynamic programming specifically, that framing is useful, because DP is exactly the topic where a problem bank leaves you stranded. LeetCode gives you 3,000+ problems and no signal about which DP state to reach for. AlgoMonster's approach is the inverse: define the pattern, show the template, then practice variations on it.

What it actually contains, per the plan page:

- All patterns, lessons, and problems unlocked
- Unlimited AI assistant usage
- Unlimited Speedrun (timed review mode)
- Real questions from top tech companies, updated monthly
- 500+ lessons across 6 courses, 1,000+ illustrations, 3,000+ editorials
- Member discount on live bootcamps
- Bonus Foundation and "Build an LLM" courses included

The lessons are text and diagrams, not video — closer to an interactive textbook than a lecture series. For DP that's arguably the better format, since watching someone derive `dp[i][j]` on a whiteboard doesn't transfer much. Seeing the table fill in cell by cell does. Whether it clicks for you is genuinely personal, and the free tier exists so you can find out before paying.

👉 [Start with the free AlgoMonster tier and read a dynamic programming lesson first](https://algo.monster?fpr=li69)

## AlgoMonster pricing: every plan on the current page

There's no monthly option. The pricing page lists three tiers, and the paid cards carry promotional pricing that changes with sales:

| Plan | Core configuration | Price | Billing period | Get it |
| --- | --- | --- | --- | --- |
| Free | Limited access to a subset of patterns, lessons, and problems | $0 | — | [Open the free plan](https://algo.monster?fpr=li69) |
| Pro – Yearly | All patterns, lessons and problems; unlimited AI assistant; unlimited Speedrun; company questions updated monthly; 500+ lessons across 6 courses, 1,000+ illustrations, 3,000+ editorials; bonus Foundation and Build an LLM courses; member discount on live bootcamps | $99/year (shown as $8.25/mo, $0.28/day) | Billed annually, 12 months of access, cancel or upgrade anytime | [Check the current yearly price](https://algo.monster?fpr=li69) |
| Pro – Lifetime | Everything in the annual plan plus lifetime content updates | $189 one-time (their framing: $0.02/day across a 30-year career) | Billed once | [See the lifetime plan](https://algo.monster?fpr=li69) |

Two things worth knowing before you click through.

First, the numbers above are what the plan cards display at the time of writing, and AlgoMonster runs frequent sales. The card on the checkout page is the authoritative price — always read it before paying, rather than trusting a number from a blog post, including this one.

Second, third-party coupon sites list stackable codes (one widely circulated one claims an extra 10% on top of the current sale, and others list a 20% code). These change and expire, and there's no way to confirm from outside whether a given code still applies at the moment you buy. If you find one, apply it and check the subtotal changed before submitting payment. Don't count on it.

You can verify the live numbers here: 👉 [See AlgoMonster's current plans and pricing](https://algo.monster?fpr=li69)

## What AlgoMonster doesn't do well

Being straight about the gaps matters more than the pitch, because these are the reasons people feel they wasted money.

**No live interview simulation.** Every problem is solved at your own pace, with no timer pressure and no interviewer asking follow-ups. Pattern recognition is necessary for a coding screen; performing it under observation is a separate skill. AlgoMonster builds the first, not the second.

**No video.** If you learn by watching someone talk through a solution, this will feel dry. That's a design choice, not an oversight — but it's still a mismatch for some learners.

**System design is introductory.** Enough for junior and mid-level DSA-focused loops, not enough for L5+ rounds where system design is a full onsite component. Plan on a separate resource for those.

**The problem count is smaller than LeetCode's.** Around 230 problems versus thousands. For DP, this is mostly fine — the bottleneck is state definition, not volume. For companies known for pulling from a huge pool, expect to supplement with volume practice elsewhere.

**Even the DP chapter has critics.** A thread on AlgoMonster's own community forum has a user preparing for Google saying the DP chapter isn't treated as in depth as the other chapters. That's one person's assessment, not a consensus — but it's a real report from inside their community, and it's worth weighing if DP is your single biggest gap and Google is your target.

None of that makes it a bad purchase. It makes it a specific purchase: strong if your gap is *which pattern does this problem need*, weak if your gap is interview pressure, system design, or raw volume.

## Which plan to pick

If you have an interview in the next two to three months, take the yearly plan. You get a full twelve months, it costs less than a year of LeetCode Premium, and the money question is settled quickly.

If you expect to interview repeatedly — a promotion cycle, a job hop every couple of years, mentoring juniors later — the lifetime plan pays for itself after roughly two years of not renewing. That's the honest math, and it's why most reviewers land on lifetime for anyone with a multi-year horizon.

If you're unsure whether the text-and-diagram format works for you, don't guess. The free tier is there precisely for that.

👉 [Compare the yearly and lifetime plans on AlgoMonster](https://algo.monster?fpr=li69)

## Common questions about DP prep

**How many DP problems do I need to solve?**
Fewer than you think, if you're deliberate about it. Two or three well-chosen problems per pattern, worked from recurrence derivation through space optimization, beats forty random problems. The goal is that a new problem triggers pattern recognition in the first two minutes.

**Should I learn DP before or after graphs and trees?**
After, usually. DP on trees and DP on graphs assume you're already comfortable with traversal. The 1D and 2D table patterns — linear DP, knapsack, string DP — can come earlier because they don't depend on graph intuition.

**Is greedy ever the right answer when DP is possible?**
Yes, when the greedy choice property genuinely holds. The trap is assuming it holds because the greedy solution passes the sample cases. If you can't state *why* the local choice is safe, DP is the defensible answer.

**Do I need a specific language?**
No. AlgoMonster's templates and solutions are available in several languages including Python, Java, JavaScript, C++, and Go. The patterns are language-independent; pick the one you'd actually interview in.

## The short version

Dynamic programming rewards a specific skill: recognizing the shape, naming the state in one clear sentence, and letting the recurrence fall out of that. Everything else — memoization, tabulation, rolling arrays — is mechanical once those two steps are right, and it's a skill you build by category rather than by volume.

If your problem is that DP problems all look different from each other, a pattern-based curriculum is a reasonable fix. Work through the DP patterns, do the two or three problems attached to each, and then convert a few top-down solutions to bottom-up by hand until the iteration order stops feeling arbitrary.

Then go practice on a problem bank, so you meet DP problems without the label attached. That's the part no course can do for you.

👉 [Get started with pattern-based DP practice on AlgoMonster](https://algo.monster?fpr=li69)
