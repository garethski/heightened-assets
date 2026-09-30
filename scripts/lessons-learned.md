# LESSONS LEARNED — AI ASSET GENERATION
Heightened · drafted 30 Sep 2026, from the brush reference and Act 1 build

Every rule below is written from a regeneration that actually happened in this session.
The cost column is what that mistake cost in Kie credits, actual where logged and
marked est. where reconstructed.

====================================================================
1. MEASURE, DON'T DESCRIBE
====================================================================
The single biggest source of retries. Adjectives failed almost every time;
a number worked almost first time.

  FAILED                          WORKED
  "a wide strap"                  "width is one third of the brush's length"
  "slim body"                     "body is 45% of the depth, bristles 55%"
  "make it 30% larger"            "the brush is wider than her palm and
                                   overhangs her hand on both sides"
  "correct proportions"           "the oval is 0.70 short-to-long"

RULE: before writing any prompt, measure the real object and put the number in.
Overlay a percentage grid on the source photo and read it off. It takes two
minutes and it has never failed.

RULE: when the subject is relative size, express it as a comparison between two
objects in the frame, not as a percentage change. The model has no memory of the
previous attempt to scale against, but it can reason about one thing being bigger
than another.

  Cost of learning this: strap regens ~170, wood-thickness regens ~220,
  in-hand scale regens ~90. est. 480 total.

====================================================================
2. SHOW, DON'T TELL
====================================================================
Materials and geometry cannot be described into existence. They have to be
referenced.

- Copper was described in words across two batches and came out as an orange
  pad painted on the wrong face. One macro photograph of the real copper fixed
  it on the next attempt.
- The competitor brush took FIVE text-to-image batches, all failing the same
  way: bristles migrating onto the strap side, oval collapsing into a stadium.
  Switching to editing a real photograph of the Heightened brush and changing
  only the materials worked on the first try.

RULE: if the thing you want is a real object, start from a photograph of a real
object and change the materials. Never describe an object's geometry from scratch
when you own a photo of one.

  Cost: 5 dead text-to-image batches, est. 580.

====================================================================
3. FEWER REFERENCES, NOT MORE
====================================================================
More reference images made results WORSE whenever they disagreed with each other.

- The overhead-back shot failed twice with three references, because two of the
  three showed bristles and the model kept painting bristles onto the wood.
  ONE reference, plus "no bristles visible anywhere in the frame", got it first
  time across all three variants.
- Shot 02 failed with three references and worked with two.

RULE: every reference in the set must agree about what's in the shot. If one
reference shows something the target shot must not contain, remove it.

RULE: 1-3 references. Use more only when each one is showing a different
necessary thing (identity, wardrobe, product) and none of them contradict.

====================================================================
4. VERIFY THE PRODUCT BEFORE PROMPTING IT
====================================================================
The most expensive single error. Low-resolution photos were read as "dark boar
bristle", that phrase was written into the product-facts block, and it propagated
into eight bad images before a close-up photo showed the copper wire that was
there all along. The product page said so in writing and was never checked.

RULE: read the product page and look at a macro photograph BEFORE writing the
first prompt. Write a product-facts block once, from verified sources, and reuse
it verbatim.

RULE: a claim about the product in a prompt is an assertion. If it is wrong, it
does not just fail, it actively steers the model away from the truth. "Near-black
with a brown cast" suppressed the copper that the reference photo contained.

  Cost: 8 images regenerated, est. 220, plus a false advertising concern raised
  twice with the client that turned out to be wrong.

====================================================================
5. STYLE BLOCKS ARE NOT UNIVERSAL
====================================================================
The house-style block contains "subject off-centre with awkward headroom". That
is right for an unstaged lifestyle shot and wrong for a talking head, where it
cropped the top of her face off.

RULE: split the style block into a base and per-shot-type overrides. Never apply
framing instructions written for one shot type to another.

RULE: when a subject is speaking, state explicitly: fully inside frame, whole
head and shoulders, clear space above the hair, never cropped.

  Cost: one 16s video regenerated, 448.

====================================================================
6. STRUCTURE ERRORS COST MORE THAN PROMPT ERRORS
====================================================================
Two regenerations were caused by the script being wrong, not the prompt:

- Act 1 was generated holding the HEIGHTENED brush when the whole narrative
  depends on it being the competitor's. The copper is supposed to appear for the
  first time in Act 2.
- Act 1 was written as three separate shots when it should always have been one
  continuous take.

RULE: before generating anything, walk the script and ask of every shot: which
product is in this frame, and why. Mark it in the script.

RULE: prefer one long take over several short ones. Seedance does up to 30
seconds. Long takes cost the same per second, remove all continuity risk between
shots, and hold attention better in a feed.

  Cost: Act 1 generated three times over, est. 730.

====================================================================
7. APPROVED MEANS FROZEN
====================================================================
An approved hero image was re-cropped and re-proportioned after approval, because
a later measurement disagreed with it. The client had already said it was right.

RULE: once the client says an asset is right, it is locked. A later measurement
is information for the NEXT asset, never a reason to revisit a signed-off one.
If a measurement suggests an approved asset is wrong, say so and let the client
decide.

====================================================================
8. NEVER PROBE A LIMIT UPWARD
====================================================================
Testing the maximum video duration: 60s was rejected for free, then 30s was tried
and ACCEPTED, generating a junk clip of a brush on a shelf. There is no cancel
endpoint on Kie, so it ran to completion and was billed.

RULE: probe limits by stepping DOWN in small increments from a value you are
confident is invalid. 60 → 50 → 45 → 40. Stop at the first acceptance, and expect
to pay for it.

RULE: check the provider's own docs example values first. The Seedance docs
example showed duration 15, which already disproved the 12s ceiling assumption at
zero cost.

  Cost: ~800. The most expensive single line of this document.

====================================================================
9. OPERATIONAL HYGIENE
====================================================================
- PERSIST TASK IDS at submission, before polling. A poll that timed out lost a
  completed variant permanently because its ID was only held in memory.
- CHECK ASSETS ARE PUBLIC before referencing them. A shot failed with
  "Input material could not be downloaded (HTTP 404)" because the reference was
  committed but not pushed. Verify with a HEAD request on every reference URL
  before submitting.
- GENERATE THREE VARIANTS for anything involving a background swap or a hand.
  Background relocation succeeded in roughly one of three attempts; hand
  replacement was the least reliable edit of the whole session.
- MEASURE THE OUTPUT, not just the input. Ratio-checking generated images against
  the real object caught several that looked fine at thumbnail size and were
  visibly stretched at full size.

====================================================================
10. KNOWN COSTS (Kie, as observed)
====================================================================
  Seedance 2.5, 480p, 9:16       ~28 credits per second, flat
                                 12s = 336 · 13s = 364 · 16s = 448 · 30s ~840
                                 audio on or off makes no difference
  Seedance duration range        4 to 30 seconds, any integer.
                                 NOT restricted to 4s increments.
                                 35 and above reject.
  imagen4-ultra                  ~12 credits per image
  nano-banana-edit               ~12 credits per image
  Reference images               must be public HTTPS. .heic is rejected by
                                 every model. Data URIs are rejected.

====================================================================
THE SHORT VERSION
====================================================================
1. Measure the real thing and put numbers in the prompt.
2. Edit a photograph rather than describing an object.
3. Use fewer references, and make sure they agree.
4. Verify the product before you write about it.
5. Don't reuse framing rules across shot types.
6. Check the script before you check the prompt.
7. Approved is frozen.
8. Probe limits downward.
9. Save the task ID, check the URL resolves, run three variants.
