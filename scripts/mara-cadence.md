# MARA — SPEECH CADENCE SPEC
Measured 30 Sep 2026 from five Act 1 generations, energy-based VAD at 6% of peak,
200ms gap closing.

## THE FINDING

Cadence is NOT fixed. Seedance compresses speech to fit the clip length.

  clip              words   speech   words/sec   verdict
  act1-oneshot 12s    20     4.68s     4.27      rushed
  act1-24h     12s    20     4.98s     4.02      rushed
  act1-16s     16s    20     4.94s     4.05      rushed
  act1-13s     13s    21     6.34s     3.31      acceptable
  act1-final   12s    21     7.06s     2.97      APPROVED

Same voice, same persona, same prompt family, 44% spread. The model speeds her
up when the word count is high relative to the time available, and it sounds
unnatural when it does.

## TARGET

  3.0 words per second of speech.

That is the approved take. Anything at or above 4.0 w/s reads as rushed
presenting rather than talking to a friend.

## PLANNING ARITHMETIC

  speech seconds   = words / 3.0
  action seconds   = 1.6 per physical beat (turning, setting an object down,
                     picking something up)
  clip length      = speech + actions + any held or timelapse tail

Worked example, the approved Act 1:

  line 1   "Your dry brush is damp for about an hour a day."        10 words
  line 2   "And it sits on that shelf for the other twenty-three."  11 words
                                                                    21 words
  speech   21 / 3.0                                         =  7.0s
  action   one turn-and-place beat                          =  1.6s
  tail     timelapse                                        =  3.4s
  total                                                     = 12.0s   <- matches

Measured structure of the approved take:
  line 1      0.30 - 3.28   (2.98s for 10 words)
  turn/place  3.28 - 4.90   (1.62s, no speech)
  line 2      4.90 - 7.70   (2.80s for 11 words)
  timelapse   8.10 - 12.00  (3.90s)

## RULES

1. Budget 3.0 w/s BEFORE writing the prompt. If the words don't fit the clip
   length, cut words or lengthen the clip. Do not let the model solve it, because
   it solves it by rushing her.

2. Put the pace in the prompt explicitly: state the approximate duration of each
   speaking beat in seconds, not just the line.

3. A 10-word line is about 3 seconds. Use that as the unit when drafting.

4. Leave 1.6s of silence for every physical action. Speech over an action
   compresses both.

5. Re-measure after any voice or model change. This spec is specific to the
   Seedance-generated voice at 480p as of Sep 2026.
