# Seedance 2.5 prompting reference

Collated from ByteDance's published formula, fal's developer guide, and a
practitioner guide written after 100+ hours on the model. Checked against our own
measured results on the Heightened "Twenty-Three Hours" film.

---

## Hard numbers

| Constraint | Value |
|---|---|
| Prompt length, narrative | **under 3,500 characters** |
| Beats per 15 seconds, narrative | **3 to 4 maximum** |
| Motions per clip | **1 to 2** |
| Reference images, narrative | **4 to 10** |
| Reference ceiling | 30 images, 10 video, 10 audio (<=8 image subjects) |
| Generation sweet spot | 15 to 30 seconds |
| Character sheet | 3 angles of the face |
| Environment sheet | 2 to 3 angles |

Montage work deliberately breaks the beat rule (13 beats in 15s is normal there).
Narrative does not.

---

## Structure

ByteDance's formula:

> Subject + Action or event + Setting and environment + Visual style + Camera work + Sound

Expanded, macro to micro:

```
SHOT        one line: what this is and how long
REFERENCES  every reference, named, with its single job and its exclusions
CHARACTER   who they are, how they carry themselves, stability requirements
SETTING     where, when, what light
CAMERA      shot size, moves, framing rules
SEQUENCE    timed beats, each with action + dialogue + diegetic sound
STYLE       the look, identical across every clip in a project
NEGATIVES   only failures that have actually occurred
```

---

## The rules that matter most

**Economy beats detail.** "A precise two-sentence prompt beats a vague five-sentence
one." Length is not the lever; precision is.

**Extra runtime does not slow delivery.** "Give it one small movement and the extra
time usually turns into waiting, repetition, or slow motion." Lengthening a clip to
slow speech buys dead air instead. Confirmed on our own clips: 17s produced a 3.2s
silent hole rather than slower speech.

**Too many actions produce stutter and morphing.** Three unrelated motions in one clip
is the documented failure threshold. Our brush deforming under a hand was this, not bad
wording.

**Each beat inherits the previous beat's physical state.** Write beats as cause and
effect, not as independent descriptions.

**Emotion is body movement.** Never "she is tired". Write "the shoulders drop, the jaw
slackens".

**Physics, not vibes.** Every action has trajectory, distance, impact and reaction.

**Sound inline, per beat.** Diegetic only. No music in the generation; add it in the
edit. Native sound effects are good; native dialogue is the weak part.

**Every reference gets one job and explicit exclusions.** "@image1 controls only the
brush. Do not copy @image1's background." An unlabelled reference pile leaks
backgrounds, wardrobe and framing you did not ask for.

**Negatives are legitimate** — ByteDance's own examples use them — but list only
problems that have actually shown up. Speculative negatives add instruction load for
nothing.

**Aggregate, do not one-shot.** Generate more than you think you need and keep the best
moments.

**Known weakness: text in frame.** Do not put words on screen.

---

## Hand and object manipulation

The single most useful technique found. Break any physical interaction into four
contact phases rather than describing it as one gesture:

```
APPROACH         which limb, from where, how fast
CONTACT          exactly what touches exactly what
FORCE TRANSFER   where the force enters, where it travels, how the material answers
RECOVERY         what stops, what stays displaced, what springs back
```

"The exact limb matters, as does where the weight goes next."

Worked example, parting soft bristles:

> Her free hand approaches and its fingertips settle on the white plastic rim at one
> edge, thumb opposite. Both press inward. The force travels through the rim into the
> bristle bed; the soft bristles buckle away from the pressure and a dark furrow opens
> down the centre. Her hand stops. The pressure holds and the furrow stays open.

Versus what we ran three times and got morphing from:

> She pushes its bristles apart with her fingers, opening a gap.

---

## Parameters (Kie market API, `bytedance/seedance-2-5`)

| Parameter | Notes |
|---|---|
| `prompt` | as above |
| `duration` | any integer 4-30; 35+ rejects |
| `resolution` | 480p / 720p / 1080p |
| `aspect_ratio` | must be `adaptive` when using first/last frame |
| `first_frame_url` | generation begins on this exact frame |
| `last_frame_url` | pins the closing frame |
| `reference_image_urls` | array |
| `reference_video_urls` | array, up to 10 |
| `reference_audio_urls` | array; **controls voice, accent and pace** |

Costs: 480p is ~28 credits/sec, ~17 with video input. Validation errors are free.

---

## Measured on our own footage

**Cadence tracks word density**, roughly:

```
density    = words / clip seconds
spoken w/s ~ 0.57 + 1.49 x density
```

| Words | Clip | Density | Delivered |
|---|---|---|---|
| 21 | 12s | 1.75 | 2.97 w/s |
| 30 | 15s | 2.00 | 3.83 w/s |
| 47 | 19s | 2.47 | 4.34 w/s |
| 47 | 16s | 2.94 | 5.06 w/s |

Directional, not precise: identical prompts have landed 3.44 and 4.93. Attaching a
voice reference steadied it more than any wording did.

**Gestures arrive about a beat late** unless cued to begin half a second before their
word. Stating that as a rule took gesture accuracy from 6/8 to 8/8.

**Accent is not steerable by prompt text.** Writing "American accent" was ignored three
times out of four. A reference audio clip fixed it first time.

**Frame position is not steerable** by prompt or reference. Sides came out mirrored
across five takes regardless. Mirror in post if it matters.

**Objects warp as soon as they rotate** past the angles the references cover. Keep
gestures to raise, lower and tip.

---

## Cutting between generations

Two independent generations never match. Three fixes, which compose:

1. **Chain frames.** `first_frame_url` set to the previous clip's last frame. Not a cut
   at all.
2. **End where there is nothing to compare.** Push into the product until it fills the
   frame. The next clip can then begin anywhere.
3. **Soften in the edit.** Cut on movement rather than on a held frame (measure
   frame-to-frame difference and cut while it is still falling), and let the incoming
   audio arrive first. If the incoming clip has no silent head, borrow room tone from a
   gap later in the same clip.

A J-cut plus cut-on-movement is invisible. A dissolve or white dip both announce
themselves.

---

## Cost discipline

- Probe any unknown on the shortest clip the model allows. Four seconds answers most
  questions for a quarter of the price.
- Never guess a parameter name. A wrong one is ignored silently and still bills.
- Read the provider docs first. Two guessed spellings of `first_frame_url` cost 644
  credits before one search found the right name.
- Fix it in post before fixing it with credits. Speed, trims, transitions, audio timing,
  mirroring and colour are all free.
- Short chained clips beat one long take: a failed beat costs a quarter as much to
  re-roll, and the rest stay banked.
