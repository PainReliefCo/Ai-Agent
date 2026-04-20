# Rosie Live App v3 — Master Handoff Brief

## 1. Project purpose

Rosie is intended to become a polished, friendly, emotionally responsive 2D avatar assistant that can be used inside a live chat application. The target outcome is a character that feels warm, expressive, trustworthy, and “alive” rather than simply animated.

The project goal is not just a static mascot. It is a production-ready real-time avatar system combining:
- layered 2D sprite animation
- mood and expression switching
- gesture playback
- idle presence behaviour
- speech-driven mouth movement
- backend-driven conversational state changes

This brief is designed so a professional animator, motion designer, frontend developer, or technical artist can quickly understand:
- what exists now
- what has already been attempted
- what the unresolved issues are
- how the system should evolve into a finished production animation/avatar product

---

## 2. Current product vision

### Intended user experience
Rosie should feel like:
- a warm AI guide
- a helpful animated assistant
- emotionally aware and visually consistent
- expressive but not cartoon-chaotic
- polished enough to be embedded in a consumer-facing product

### Intended personality
Rosie should communicate:
- friendliness
- patience
- warmth
- curiosity
- confidence without stiffness
- subtle emotional responsiveness

### Visual direction
The original mock-up establishes the intended look:
- soft, rounded, feminine robot design
- deep green body
- bright expressive eyes
- small antenna with glow and breathing space above the head
- gentle charm rather than hard sci-fi
- soft lighting and soft compositing rather than harsh cut-out sprite edges

---

## 3. Current app architecture

### Frontend
- React / TypeScript frontend
- Vite dev server
- layered 2D avatar rendered with positioned image sprites
- Framer Motion used for body/arm/head/FX animation
- separate component for Rosie avatar assembly

### Backend
- Node / Express backend
- WebSocket support for live Rosie state events
- chat endpoint for conversational turn handling
- backend health endpoint
- local development endpoint on port 8787

### High-level runtime structure
- `App.tsx` manages layout, message flow, backend connection, and avatar placement
- `RosieAvatar.tsx` assembles the avatar from layered sprite images
- `useRosieController` manages overall emotional/state logic
- `useGestureController` handles gesture selection and playback
- `usePresenceSystem` drives idle behaviour
- `useRosieSpeech` handles speech-related state and voice timing
- `useSpeechMouth` maps speech activity/audio to mouth sprites
- `useBlink` controls eye blinking
- sprite assets live under public avatar directories and are layered into one composed avatar

---

## 4. What has already been done

### A. Layout and scale fixes
Early builds rendered Rosie extremely small on screen. Work completed:
- corrected incorrect assumptions about project folder structure
- got client and server running locally
- identified that the avatar was rendering but at tiny scale
- adjusted stage layout in `App.tsx`
- introduced grounded avatar positioning
- added floor glow and grounding shadow concepts
- scaled avatar container to fill the stage more naturally

### B. RosieAvatar structural work
Work completed in `RosieAvatar.tsx`:
- wrapped layered sprite composition more cleanly
- anchored scale from the bottom center
- lifted antenna/glow placement closer to original mock-up
- softened glow behaviour
- improved isolation/compositing at the avatar frame level
- attempted to reduce jagged edge appearance via rendering tweaks

### C. Asset and composition polish attempts
Work attempted:
- trimming/cleaning image layer presentation
- upscaling some avatar assets in prior zip iterations
- reducing harshness of sprite halos with compositing/rendering changes
- softening some face and glow layers

### D. Motion / timing work attempted
Work attempted or partially implemented:
- mouth timing smoothing
- gesture timing cleanup
- state transition softening
- face swap stabilization
- attempts to remove duplicate eye rendering during mood changes

---

## 5. Main issues encountered during development

### 1. Avatar initially appeared too small
This was caused by:
- incorrect stage sizing assumptions
- the avatar’s internal sprite canvas being much larger than the visible character
- scale being controlled in the wrong layer

### 2. Antenna placement did not match original mock-up
This was caused by:
- internal layer offsets in `RosieAvatar.tsx`
- scaled canvas changing perceived head-to-antenna spacing

### 3. White/pixel halos around eyes, arms, and edges
Likely causes:
- baked edge pixels in source PNGs
- transparency/antialiasing artifacts
- scaling sprite sheets at non-native display sizes
- compositing multiple semi-transparent layers with imperfect cutouts

### 4. Face/eye duplication or glitching during state changes
This has been the most persistent issue.

Likely causes:
- multiple simultaneous state updates involving:
  - mood
  - blink
  - mouth sprite
  - speaking state
- sprite overlap or stale face frames during swaps
- `useBlink` and `useSpeechMouth` competing with emotional state changes
- face-state churn happening faster than the render is visually stable

### 5. Mouth and gesture transitions still feel rough
Current issues:
- mouth shapes can feel chatter-y or jumpy
- gesture timing can feel slightly disconnected from intent
- state transitions between idle / thinking / speaking / emotional responses are not yet fully art-directed

---

## 6. Current known technical conclusion

At the latest point in development, the strongest remaining suspected root cause for the face glitch is:

### `useBlink.ts`
This is now the most likely source of the eye duplication/glitch behaviour because:
- major render-layer overlap issues were already reduced
- face fade and `AnimatePresence`-based overlap was removed
- blinking is still the most likely competing source of eye sprite changes during interaction

Secondary possible source:
### `useSpeechMouth.ts`
This may still be contributing to face instability because mouth changes may be happening too frequently or at the same time as mood changes.

---

## 7. What the current avatar system is trying to do

Rosie currently uses layered sprites for:
- body
- wheels
- arms
- hands
- neck ring
- head shell
- ears
- face depth panel
- blush / effect overlays
- brows
- eyes
- mouth
- antenna glow
- auxiliary FX such as sparkle / sweat / question mark / zzz

### Animation approach
Current animation is sprite-compositing plus motion transforms:
- arms rotate according to selected gesture plan
- body gently moves vertically
- head area can tilt slightly
- glow scales and fades
- facial sprites swap based on mood and speaking state

This is a valid prototype architecture, but it is still in the “engineer-assembled layered puppet” stage rather than a final animator-tuned production avatar.

---

## 8. What a professional animator / technical artist should review next

A professional animator or technical artist should review the project in this order:

### Priority 1 — face-state stability
Review:
- `RosieAvatar.tsx`
- `useBlink.ts`
- `useSpeechMouth.ts`
- emotion state timing from controller hooks

Goal:
- guarantee only one face state renders at a time
- prevent blink, mood, and mouth states from fighting each other
- create a clear priority system for face states

Recommended direction:
- create a face-state state machine
- prioritize expression > blink > mouth or another deliberate hierarchy
- avoid simultaneous independent sprite swaps in the same region

### Priority 2 — sprite asset cleanup
Review all face and limb PNGs for:
- white halos
- anti-aliased transparency contamination
- inconsistent cropping/padding
- mismatched edge softness
- scale inconsistency between layers

Recommended direction:
- re-export source sprite layers cleanly from master art
- keep all face layers perfectly aligned within a shared coordinate template
- remove baked glow spill or white matte contamination

### Priority 3 — animator-grade motion pass
Review:
- gesture arc quality
- head motion timing
- breathing/idle loops
- mouth timing cadence
- expression transitions

Recommended direction:
- shift from simple transform-driven movement to more art-directed easing and anticipation
- introduce intentional micro-delays and holds
- reduce mechanical symmetry

### Priority 4 — final production rig direction
Longer-term, consider replacing manual sprite swaps with:
- a true 2D puppet rig
- layer groups for eyes/brows/mouth
- state blending or animation clips
- a timeline-based system or Spine/Rive/Live2D-style workflow if appropriate

---

## 9. Recommended production target

The production-ready Rosie should eventually include:

### Visual quality
- clean sprite edges with no white halos
- consistent lighting and compositing
- antenna and glow matching original design
- smooth expression readability at all sizes

### Motion quality
- stable mouth timing
- expressive but controlled gestures
- smooth state transitions
- subtle idle breathing and awareness

### Behaviour quality
- emotional state changes that feel deliberate
- no visual glitches during response generation
- speaking state that feels coherent with mouth and body animation
- idle state that feels alive, not frozen

---

## 10. Proposed animation/state hierarchy

A recommended hierarchy for a finished implementation:

1. **hard state**
   - idle
   - listening
   - thinking
   - speaking
   - error
   - sleepy

2. **expression state**
   - neutral
   - warm
   - curious
   - surprised
   - reassured
   - concerned

3. **blink state**
   - should be suppressed during critical face transitions or active speaking if needed

4. **mouth state**
   - phoneme or simplified speech-shape driven
   - throttled and smoothed

5. **gesture state**
   - independent but coordinated with speech/emotion

This would prevent the current “too many systems changing the face at once” problem.

---

## 11. Practical development roadmap

## Phase 1 — stability and cleanup
Goal: eliminate visible glitches and establish reliable baseline quality.

Tasks:
- fix `useBlink.ts` so blink cycles cannot overlap or fight speaking/emotion updates
- review `useSpeechMouth.ts` and reduce chatter
- create explicit priority rules for face state updates
- remove any remaining duplicate-face rendering behaviour
- validate that the avatar compiles and hot reloads reliably

Deliverable:
- stable interactive Rosie with no duplicate eyes or broken face transitions

## Phase 2 — asset cleanup and visual polish
Goal: make Rosie visually clean enough for stakeholder review.

Tasks:
- re-export face/arm layers with clean alpha
- remove white edge halos
- normalize layer dimensions and alignment
- refine antenna glow art
- refine eye graphics for crisp readability

Deliverable:
- visually clean Rosie with production-acceptable sprite assets

## Phase 3 — animation polish
Goal: move from “prototype motion” to “character performance”.

Tasks:
- animator pass on gestures
- animator pass on idle loops
- subtle breathing motion
- improved head tilt timing
- more natural gesture anticipation and settling
- emotional transitions with intentional timing

Deliverable:
- Rosie that feels alive, warm, and characterful

## Phase 4 — speech/face integration
Goal: make speech performance feel intentional.

Tasks:
- better mouth shape selection
- phoneme grouping or higher-quality viseme mapping
- emotion-aware mouth behaviour
- eye/brow behaviour synced with speech energy

Deliverable:
- speaking Rosie that feels cohesive rather than assembled

## Phase 5 — production hardening
Goal: prepare for shipping or client-facing demo use.

Tasks:
- asset optimization
- performance QA
- browser consistency pass
- animation fallback logic
- final state machine cleanup
- documented animator/developer pipeline

Deliverable:
- production-ready Rosie avatar module

---

## 12. Immediate next tasks that were planned

The next tasks we had hoped to achieve were:

1. **Fix `useBlink.ts`**
   - treat blink as the final likely root cause of eye duplication
   - reduce or suppress blink during speaking or emotional changes

2. **Polish `useSpeechMouth.ts`**
   - smooth mouth transitions
   - reduce rapid chatter between mouth frames
   - align mouth logic more closely to speaking cadence

3. **Introduce a cleaner face-state controller**
   - one source of truth for brows/eyes/mouth rather than separate independent updates

4. **Professional animation pass**
   - move from “frontend animation” to “character performance”
   - tune gestures and state changes

5. **Asset cleanup pass**
   - remove pixel halos and edge contamination in the PNGs
   - standardize all sprite exports

6. **Idle / presence system improvements**
   - look-around behaviour
   - breathing motion
   - micro reactions while waiting
   - more lifelike resting state

7. **Emotion system improvements**
   - smoother transitions between curiosity / thinking / speaking / neutral
   - reduced snapping between moods

8. **Voice and mouth sync refinement**
   - stronger correlation between audible speech cadence and mouth timing
   - eventually move toward better viseme mapping

---

## 13. Handoff recommendation

If handing this project to a professional animator or technical artist, provide them with:

- the original Rosie mock-up / concept render
- the current React/Vite project
- all sprite assets
- this document
- screen recordings of the current glitches
- a shortlist of target reference behaviours

### Suggested handoff brief in one sentence
“Please take this layered 2D React-based Rosie avatar prototype and convert it into a production-quality animated assistant with stable facial state logic, clean assets, smooth transitions, and polished emotional/speaking behaviour.”

---

## 14. Final summary

This project has successfully reached the stage of:
- working local client/server app
- live avatar on screen
- layered puppet structure
- basic emotional/speech/gesture architecture
- rough animation and presence systems

The project is not yet finished because the remaining work is now in the most difficult layer:
- state coordination
- asset cleanup
- animation polish
- character performance

That is normal. The prototype has already proven the architecture direction. The remaining work is the professionalization pass.

The key unfinished blocker is:
### face stability under live state changes

Once that is solved cleanly, Rosie can move rapidly from prototype to polished interactive character.
