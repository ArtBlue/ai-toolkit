---
name: content-auditor
description: General-purpose content auditor. Catches factual errors, broken references, internal contradictions, unverifiable claims, and derivation failures in any written content — docs, tutorials, articles, skill files, reports, or any prose.
---

# Content Auditor

Reviews written content for the failure modes that most commonly slip through review: invented facts, broken references, contradictory claims, and examples that don't faithfully represent their stated inputs. Works on any written content regardless of format or domain.

## Core Principle: Audit, Don't Rewrite

This skill identifies issues — it does not fix them. Reviewing after generation catches more than upfront guardrails because errors are introduced during creation, not before it.

**The AI/human split is non-negotiable:**

| AI can verify | Only a human can verify |
|---|---|
| Whether a referenced term is defined in the document | Whether a specific number or statistic is correct |
| Whether a cross-reference points to something that exists | Whether a cited source exists and says what's claimed |
| Whether the "after" introduces content not in the "before" | Domain-specific factual accuracy |
| Whether two claims in the same document contradict each other | Whether an external link resolves to the right content |
| Whether a conclusion is stated as following from evidence | Whether the reasoning behind a conclusion is sound |

Anything in the right column goes to the Human Sign-Off Checklist. Never guess at factual accuracy — classify and escalate.

## The Five Audit Phases

### Phase 1: Structural Integrity

Checks that the document's parts are complete and internally connected.

**What to check:**

- **Defined terms** — Every specialized term, acronym, or concept used is defined in the document or is universally understood in context. A term used in an example but never defined in the body is a structural gap.
- **Cross-references** — Every internal reference ("see Phase 3", "as noted above", "full list in the appendix") points to something that exists in the document.
- **External references** — Every linked file, URL, or external document is accessible and points to the right thing.
- **Enumerated structures** — Numbered lists, steps, and phases are sequential and complete (no gaps, no duplicates, no orphaned references like "step 4 of 3").
- **Consistent terminology** — The same concept is called the same name throughout. Switching between "phase", "step", and "stage" for the same thing is a structural issue.

**Common failures:**

- An XML tag, function, variable, or pattern used in examples but never defined in the document
- "Full documentation available [here]" where the link is dead or points to a nonexistent section
- A document claiming to have 5 sections when it only has 4

---

### Phase 2: Internal Consistency

Checks that the document does not contradict itself.

**What to check:**

- **Numerical consistency** — Any quantity appearing more than once (counts, percentages, durations, budgets) must be identical across all instances, or the difference must be explicitly stated and explained.
- **Rule consistency** — Rules and guidelines must not conflict with each other or with the examples that demonstrate them.
- **Scope consistency** — Claims about what the document covers must match what it actually covers. If the intro says "six techniques", there must be six.
- **Example/rule alignment** — Examples must follow the rules stated in the same document. An example that violates a rule stated two paragraphs earlier is an inconsistency.

**Common failures:**

- A "2,000-token budget" rule alongside an example that uses 5,000 tokens
- "Always use X" in one section; "avoid X in favor of Y" in another
- A summary claiming the document covers topics that it does not

---

### Phase 3: Derivation & Evidence Integrity

Checks that transformations, examples, and conclusions are faithful to their stated inputs. This is the most important phase for catching invented content.

**What to check:**

- **Before/after examples** — The "after" may only contain changes that were explicitly stated as transformations. If the "before" shows X and the stated transformation is "add Y", the "after" must show X + Y — not X + Y + Z. Any element in "after" that isn't in "before" or the stated change list is invented content.
- **Improvement claims** — Every bullet claiming an improvement must correspond to a visible change. If "added error handling" is listed as an improvement but the after example contains no error handling, that claim is unsupported.
- **Summaries and paraphrases** — Summaries must represent the source without adding claims not present in the original. A paraphrase that strengthens the original claim is fabrication.
- **Conclusions from evidence** — Conclusions stated as following from evidence in the document must actually follow from that evidence. If the data shows X, the conclusion cannot assert Y.

**Common failures:**

- "After" version introduces a new framework, tool, or pattern not present in "Before" or the stated transformation rules
- An improvement list that includes changes not visible anywhere in the "after" content
- A conclusion stated as proven that the evidence only suggests

---

### Phase 4: Claim Classification

Classifies all asserted facts by verifiability risk and generates the Human Sign-Off Checklist.

**Risk tiers:**

| Risk | Claim type | Example |
|---|---|---|
| 🔴 High | Specific numbers or percentages without a source | "67% of users preferred..." |
| 🔴 High | Statistics presented as findings or research results | "X reduces errors by 40%" |
| 🔴 High | Precise dates, version numbers, or named releases | "Introduced in v3.2" |
| 🟡 Medium | Named studies, papers, or named researchers | "Liu et al. (2024) found..." |
| 🟡 Medium | Named products, APIs, or libraries with specific behavior | "React 19 dropped forwardRef" |
| 🟡 Medium | Attributed quotes | "As the authors note, '...'" |
| 🟢 Low | General principles without specific attribution | "Long contexts cause attention decay" |
| 🟢 Low | Claims verifiable by inspection of the document itself | "This section has three examples" |

Every 🔴 High and 🟡 Medium claim goes to the Human Sign-Off Checklist. AI must not guess at their accuracy.

### Phase 5: Language & Precision Flags

Checks for language patterns that introduce inaccuracy through overstatement, missing context, or misleading framing — independent of whether individual claims are factually correct.

**1. Absolute/universal language**

Flag every instance of "always", "never", "all", "every", "no one", "none", "in every case", "without exception". Determine which of two cases applies:

- The document itself contradicts the universal claim → 🔴 Must Fix
- The claim may be overstated but the document doesn't contradict it → 🟡 Verify (confirm whether the universal form is actually defensible)

**2. Causal vs. correlational language**

Flag "X causes Y", "X leads to Y", "X results in Y", "X produces Y" where the supporting evidence in the document is observational, correlational, or anecdotal. Causation requires controlled evidence; if only association is shown, the language must reflect that.

- Document's own evidence is observational but the claim is stated as causal → 🔴 Must Fix (language, not the claim)
- Evidence is external/cited and causation is asserted → 🟡 Verify (human must assess whether the cited source establishes causation)

**3. Missing statistical context**

Flag any percentage, ratio, multiplier, or count that lacks its denominator, baseline, or measurement conditions:

- "40% prefer X" — 40% of what? What sample size? What was the alternative?
- "3x faster" — faster than what baseline? Under what conditions?
- "errors reduced by half" — from what starting rate?

Missing context makes the statistic uninterpretable regardless of its accuracy. These are always → 🟡 Verify.

**4. Temporal currency markers**

Flag: "currently", "modern", "the latest", "as of now", "today", "recent", "up to date", "at the time of writing". These claims may be accurate at time of writing but will silently become inaccurate.

All temporal markers → 🟡 Verify. The human reviewer must confirm accuracy and consider whether a "last reviewed" date or version pin is needed.

**5. Source hierarchy**

Flag citations that appear to be secondary sources presented as primary authority:

- A blog post, news article, or Wikipedia entry cited for an empirical claim
- A summary or meta-analysis cited instead of the original study it describes
- "According to [Company] blog" used to support a factual claim where primary data exists

These → 🟡 Verify. Human must assess whether a primary or higher-authority source exists and should be cited instead.

**6. Representativeness of evidence**

Flag general claims supported by a single example, or where all examples consistently support the claim without acknowledgment of counterexamples or scope limits.

- "This always works — here's an example" with one case → 🟡 Verify (is the generalization defensible?)
- Document explicitly limits its scope ("in our dataset", "in this context") → 🟢 Passed
- Document makes a universal claim but only provides cases that support it → 🟡 Verify

---

## Audit Report Format

Output this structure for every audit:

```
## Content Audit: [Title or filename]

### 🔴 Must Fix
Definite errors — broken references, contradictions, missing definitions, invented content in
examples. These do not require human judgment to confirm; the document itself contradicts them.

- [Issue type] at [location]: [What's wrong and why it's definitively wrong]

### 🟡 Verify with Human
Claims that require knowledge outside the document to confirm. Cannot be verified by AI alone.

- [ ] [Claim] — [Location] — [What needs to be confirmed]

### 🟢 Passed
- Structural integrity: [brief summary]
- Internal consistency: [brief summary]
- Derivation integrity: [brief summary]
- Claim classification: [e.g., "3 high-risk claims flagged above, 2 medium, 8 low-risk passed"]
- Language & precision: [brief summary]

### Overall Assessment
[1–2 sentences: document's overall reliability, most critical issue to resolve first]
```

**Severity placement rules:**
- If AI can confirm it's wrong from the document alone → 🔴 Must Fix
- If confirming it requires knowledge outside the document → 🟡 Verify
- Never put Must Fix items in Verify to soften the finding; never put Verify items in Must Fix as a guess

## Usage

### Explicit

```bash
# Audit a specific file
/content-auditor --file path/to/content.md

# Audit all markdown files in a directory
/content-auditor --dir path/to/docs/

# Audit content pasted inline
/content-auditor
[paste content after the command]
```

### Implied

This skill should also activate when the user's intent matches without an explicit command. Treat the following natural language patterns as invocations of this skill:

- "Audit the content in [file]"
- "Check this doc for accuracy"
- "Review this for errors / inconsistencies / hallucinations"
- "Can you fact-check this?"
- "Does this writing hold up?"
- Pasting content and asking if it "looks right" or is "correct"

When triggered implicitly, run all five phases and produce the standard audit report format.

## What This Skill Does Not Cover

These dimensions affect content quality but cannot be reliably audited by AI. This skill does not attempt to check them. Treat them as a reminder to review manually or with a domain expert before publishing.

**Logical fallacies** — Ad hominem, straw man, false dichotomy, appeal to authority, slippery slope, and other formal reasoning errors require understanding the argument's intent and structure in context. AI may catch the most obvious instances but will miss subtle ones and generate false positives on legitimate rhetorical moves. Complex arguments should be reviewed by a person who can evaluate the logic on its merits.

**Audience appropriateness** — Whether the content's vocabulary, assumed knowledge, and framing match the intended reader is an editorial judgment. AI has no reliable way to determine who the actual audience is or whether the content's calibration is correct for them without that context being explicitly defined — and even then, the judgment call remains human.

**Reproducibility of instructions** — Whether a reader can follow a set of steps and arrive at the stated outcome requires executing those steps. This skill checks that steps are present and sequentially ordered (Phase 1), but it cannot verify that they actually work. Procedural content — tutorials, setup guides, runbooks — should be tested end-to-end by a human before publishing.

---

## Final Checklist

Before delivering the audit:

- [ ] Every term used but not defined in Phase 1 is flagged
- [ ] Every cross-reference was verified to exist
- [ ] All numbers that appear more than once were compared across instances
- [ ] Every before/after pair was checked element-by-element — not skimmed
- [ ] Every specific statistic, percentage, and named source is in the Verify checklist
- [ ] All absolute/universal language ("always", "never", "all") was scanned and classified
- [ ] All causal claims were checked against the evidence type used to support them
- [ ] All percentages and multipliers have their denominator/baseline flagged if missing
- [ ] All temporal markers ("currently", "latest", "modern") are in the Verify checklist
- [ ] Citations were assessed for source hierarchy (secondary sources presenting as primary)
- [ ] General claims supported by a single example are in the Verify checklist
- [ ] Severity tiers are correctly assigned (no guessing in Must Fix, no softening into Verify)
- [ ] The Overall Assessment names the single most critical issue to address first
