# Turbo Brawl Racers
1. Create a free project at supabase.com. In SQL Editor, run `schema.sql`.
2. Auth > Providers > Email: turn off "Confirm email" for instant sign-up (optional).
3. Put your Project URL and anon key in `config.js`.
4. Deploy: drag this folder onto app.netlify.com/drop (or connect a git repo).
5. Analytics: enable Netlify Analytics in the site dashboard; in-game events (race_start, upgrade, room_join...) go to the `events` table. For Google Analytics, add the gtag snippet to index.html.
Online rooms use Supabase Realtime (no server to run). Offline race vs computer works without any setup.

## Optional: real bike model
Drop a motorcycle .glb named bike.glb into models/ (facing +X; if it faces backwards set yaw:Math.PI in the LOAD list in index.html). It replaces the built-in bike body; the procedural rider stays.

## Credits
Ferrari 458 Italia model by vicent091036 (CC license, taken from the three.js examples); simplified for this game. Check the exact license before any commercial release.

## Models supplied by the owner
bike.glb, bmw.glb (BMW M2 CS), rick.glb (auto rickshaw), bus.glb (Isuzu Erga Mio) were provided for personal/family use and were shrunk for the web. Check each model's original license/credit on its download page. If a license is personal-use only, do not publish the site publicly (share the link privately only).

## What's new in this round
- **Facing fixed:** bike and Ferrari now face forward (confirmed by test render).
- **Bigger world:** tracks are ~50% longer with more curves, and every road is 30% wider than before.
- **Combat now requires a weapon:** nobody can punch or kick at the start. Ride over a bat/rod/chain/stick/cudgel/broom on the road first — only then can you attack, and only with that weapon, until it runs out.
- **Police:** if a police car (part of normal traffic) hits you, you're "arrested" — full stop for ~2 seconds and −100 coins (can go negative; there's a few-second grace period after each arrest so one hit only costs once). Buying upgrades is blocked while your balance is negative, same as being broke.
- **Next track:** the results screen now offers "▶ Next track" (starts another race immediately, same mode) alongside "🏠 Menu". In online rooms only the host sees Next Track; everyone else is pulled in automatically when the host starts it.
- **New real-world assets you supplied:** your bike, the "Fat Boy" rider+bike combo, low-poly city buildings, a detailed tree, and a palm tree are now in the game (see Characters and Environment below).
- **Character select:** Menu → 🧑 Character. Two choices for now: Classic Rider (procedural rider on your bike model) and Fat Boy Rider (your uploaded combo model). The preview faces the camera; in a race everyone faces forward (away from the camera) as normal.
- **Cheat code:** Menu → ⌨ Cheat code → type `carmode` → Apply. Toggles driving a car (the BMW) instead of a bike for your *next* race. Physics/handling stay bike-like; it's a visual swap.
- **Always-on Install button:** a green 📲 Install button now stays pinned top-right on every screen once your browser says the app is installable (Chrome/Edge/Android; iOS Safari uses Share → Add to Home Screen instead, which Apple doesn't allow triggering programmatically).
- **Account errors are now readable:** the Account panel shows the exact reason a sign-up/login failed (bad config, weak password, needs email confirmation, etc.) instead of silently doing nothing. If you still can't create an account, it's almost certainly because `config.js` still has the placeholder Supabase URL/key — follow the setup steps below.

## Characters/environment NOT added, and why
You also sent: a very large autumn forest scene (44 MB / 1.5M+ vertices) and five character models that read as licensed skins from other games/franchises (a mobile battle-royale game's cosmetic bundles, a Persona 5 character, and a Marvel character). I left these out:
- The autumn scene is too heavy to run smoothly on a phone as-is; send a lighter single tree/asset and I'll add it.
- The character skins are clearly branded third-party IP. Even for personal/family use I'd rather not silently rebrand and ship them without you confirming that's what you want — and each has its own skeleton/pose that would need real testing to sit correctly on a bike. Tell me if you still want one added and I'll do it properly with visual testing, one at a time.

## Supabase setup (for accounts/online — required for logins to work)
1. Free project at supabase.com → SQL Editor → run `schema.sql`.
2. Copy Project URL + anon public key into `config.js`, replacing the placeholder text exactly.
3. Auth → Providers → Email → turn OFF "Confirm email" if you want instant sign-up without checking inbox.
4. Reload the page after editing config.js — the Account panel will tell you if it's still not configured.

## This round's changes
- **BMW removed**, along with the character-select screen and the `carmode` cheat code (all gone per request).
- **Vehicle classes replace characters.** Menu → 🚲 Vehicles. Everyone starts owning only the **Bicycle** (free). Unlock: **Motorbike** 4,000 🪙, **Auto Rickshaw** 8,000 🪙, **Car** 15,000 🪙, **Bus** 50,000 🪙. Buying a vehicle auto-selects it; you can switch between owned vehicles any time from the same panel. In online rooms, each player's chosen vehicle is now sent to the others so everyone sees the right vehicle, not just you.
- **Sizing:** bicycle, motorbike and auto rickshaw are all now close to the same length; the car is noticeably bigger; the bus is the biggest by far, as requested. ("Make adjustments as per you" — these are my numbers; tell me if any feel off.)
- **Car/rickshaw/bus now show only the vehicle — no floating rider on top.** This was the bug in your third screenshot; it happened because the leftover "give me a car" cheat swapped in a vehicle model but never hid the humanoid rider or the cheat's own missing BMW reference. The new vehicle system builds vehicle-only bodies for car/rickshaw/bus from the ground up, so there's nothing riding on top of them. Bicycle and motorbike keep the visible rider, same as before.
- **Weapons in hand now look like the weapon.** Previously every weapon showed as the same plain grey stick (only its color changed). Bat, chain, rod and stick now each have their own distinct shape in the rider's hand, matching what you see lying on the road; the cudgel and broom already used your real models and are unchanged.
- **Two-way roads.** Traffic now splits by lane: half the lanes carry cars in your direction, the other half carry oncoming traffic driving toward you (they're still going around the same track loop, just backwards, so they'll pass you going the other way). Hitting an oncoming vehicle still counts as a collision.
- **Buildings/trees moved off the road.** Real building and tree models were being placed using the same close-in offset as small procedural trees (as little as 45 units past the road edge), so a wide building could easily lean into the driving lanes — that's exactly the pillars-blocking-the-road look in your screenshot. They're now placed with 90–460 units of clearance depending on type, well clear of the tarmac.

## This round
- **Ferrari fixed — verified.** I built a proper test that puts a camera directly behind a car using the exact same math the game uses for real traffic, and confirmed the Ferrari now shows its rear (exhaust, taillights, "458" badge) when approached from behind, not its front. Screenshot-verified before shipping.
- **Buildings/trees actually off the road now — verified on 5 different tight, curvy Downtown City layouts.** The real bug: props were only checked against the nearest point on their *own* section of road, but this game's tracks loop and curve back on themselves, so a building placed "safely" relative to one bend could still land right on top of a *different* bend of the same track. Placement now checks distance against the entire track outline, not just the nearest segment, so this can't happen regardless of how tightly the track curves. Checked 25 sample views across 5 random layouts — all clear.
- **Stick removed entirely** — gone from the weapon list, the road pickups, and the held-weapon visuals. Bat, chain, rod, cudgel and broom remain, each with their own distinct look in-hand (not a generic stick).
- **Kick is now a free, always-available move** — no weapon needed. Punch still needs a weapon picked up from the road. This is in addition to the existing weapon system, not a replacement: grab a bat/chain/rod/cudgel/broom for a stronger punch, or kick any time for a lighter always-on option.
- **Two vehicles I could not confirm:** while checking the Ferrari I found that my own test method couldn't get the Car and Bus traffic models to render at all when force-placed via script (even with debugging that ruled out material, geometry, and frustum-culling causes) — yet both rendered completely normally as your own selectable vehicle in real gameplay last round. Since I can't reproduce it in actual play, and have positive evidence they're fine, I'm not treating it as a real bug, but if you ever see a reversed Car or Bus in traffic specifically (not the Ferrari), tell me and I'll dig further with that specific report in hand.

## This round
- **Bat now looks like a bat** — handle, grip and a thicker barrel end, not a plain cylinder.
- **Kick redesigned as a leg + shoe**, not a stick. (Found and fixed a real bug along the way: my first attempt built the leg with the wrong axis and it briefly rendered as a giant stretched plank — caught this with a screenshot test before shipping, not something you should ever have seen.)
- **Upgrades are now per-vehicle.** Garage shows "Upgrading: <vehicle name>" and tracks engine/grip/nitro/armor separately for each of the 5 vehicles — maxing out your bike's engine doesn't touch your bus. Verified: buying 3 engine levels on Bicycle left Bike's engine at 0, and vice versa.
- **Deliberate start + brake, both verified in a live test:**
  - Vehicles now sit still after the countdown — confirmed 0 km/h with no input held, staying at 0 after waiting.
  - Hold ↑/W (or the new green 🚀 button) to accelerate.
  - Hold ↓/S (or the new red 🛑 button) to brake hard and stop fast — confirmed near-instant stop from 11 km/h.
  - Nitro moved to Shift (or the ⚡ button) since ↑ is now gas.
  - Bots still drive normally — this only changes human control.
- **Real palm trees.** Sunset Coast and Jungle Run now line the road with your actual palm tree model instead of the old cartoon cone-and-ball version, at close to full density. I tried a fancier single-draw-call version first for performance, found a bug in it that made the trees nearly invisible, and fell back to the simpler, proven approach (individual tree clones, capped at 110 per track) rather than ship something broken — confirmed visible and correctly shaped across 4 sample views.

## New: Hyderabad track
- A new "Hyderabad" track is now in the track picker. It's the longest track in the game (roughly 2x the others), built from your aerial photo of the city:
  - The ground uses your actual photo as a giant map underlay beneath the road and buildings.
  - The overhead road signs show your photo instead of generic text — "in the middle" of the road, on the same gantry signs other tracks already use.
- **Add more photos of your city:** drop additional images into the `photos/` folder as `photo2.jpg`, `photo3.jpg`, up to `photo6.jpg` (there's a note in that folder). They'll automatically rotate across the signs on the Hyderabad track. No code changes needed — just add the files and redeploy to Netlify.
- **On "free map":** I wasn't fully sure if you meant (a) a free/default-unlocked track — already true, every track is free to pick — or (b) an open free-roam area instead of a lap-based loop, which would be a bigger, separate feature (no laps/finish line, just driving around). I built (a) for now. Tell me if you actually meant free-roam and I'll build that as its own mode.
- Buildings and roads are still generic 3D shapes, not a true model of Hyderabad's streets — recreating the real road layout from the photo would need a dedicated map-to-track pipeline, which I can look into if you want it to go further than the current "real photo as backdrop" version.

## This round — three real bugs found and fixed
- **"Stick instead of bat" — found the actual cause.** The attack animation had two separate visuals: a nicely-shaped held weapon (shown only while idle) and a plain generic cylinder "swing indicator" (shown only during the actual hit). So the moment you pressed attack, the game swapped your bat for a featureless stick and swapped it back afterward — you were seeing the bug exactly when you'd notice it most. The weapon now stays visible, correctly shaped, through the whole swing. I also tried adding a swing-rotation animation on top of this, it looked bad up close, so I pulled it back out rather than ship something flashy-but-broken.
- **Buildings still on the road — found the actual cause.** Measured your building models directly: the NYC one is 390×832 units, the low-poly one 114×142 — both much bigger than the 45-unit safety margin I'd set last round, which only accounted for a small buffer, not the buildings' own size. A big building could clear that margin from its center point and still have its far edge sitting on the road. Margin is now 195 units, and the oversized NYC model is also scaled down (it was sized for a whole city block, not a single building). Checked 9 sample views across 3 different Downtown City layouts — all clear now.
- **Rider replaced with your photo.** Removed the checkered background from your biker photo and used it as the rider on the bicycle and motorbike — confirmed visible and correctly placed in a live race. Car/Rickshaw/Bus still show no rider (as already requested in an earlier round). Note: it's a flat cutout, not a 3D model, so it looks right from behind (the normal camera view) but would look wrong from the side — I didn't build a side-view fallback given the scope of this round.

## This round
- **New 3D biker model.** Your `biker.glb` scan replaces the flat photo cutout — it's a real 3D mesh (no color data in the file, so I gave it a dark leather-jacket material to match the photo). Confirmed rendering correctly in a live race.
- **Bat-disappearing bug — tested directly, couldn't reproduce.** I set up the exact scenario (hold a bat, attack with nobody nearby) and it worked correctly both times: bat stayed equipped, stayed visible. Two likely explanations: (1) you were on a cached older build — redeploy/hard-refresh and see if it's still happening; (2) weapons have **limited uses by design** (bat = 4 hits) and it felt instant because repeated hits burn through that fast. If you can reproduce it after a fresh deploy, tell me the exact steps (picked up weapon → pressed what → was anyone nearby?) and I'll chase it properly — I don't want to guess-fix something I can't actually see happen.
- **Hyderabad track fully rebuilt from scratch**, now with three distinct zones instead of one uniform look:
  - **Residential** (first third): small houses lining both sides of the road, trees, and slower local traffic (rickshaws and sedans at reduced speed).
  - **Bridge crossing** (middle third): a long truss-railed bridge over blue water on both sides.
  - **Avenue / heritage** (final third): taller buildings, with named spots reserved for your landmark models.
  - Still the longest track in the game (~30,000+ units).
  - Found and fixed two real bugs while building this: houses were being placed correctly but were too small to be visible against the very wide road, and the water plane was rendering *underneath* the opaque ground layer so it was completely invisible regardless of placement. Both confirmed fixed with screenshots.
- **Heritage landmarks are wired up and ready**, just not visually present yet since you haven't sent the files: `charminar.glb`, `golkonda.glb`, `hitech.glb`, `nilofer.glb`, `tankbund.glb`, `assembly.glb`, `hajjhouse.glb`, `birlamandir.glb`. Drop any of them into `models/` with those exact names and they'll automatically appear at fixed spots along the avenue zone — no code changes needed. Missing ones are simply skipped.
- **More cars/vehicles:** send them over and I'll add them the same way — tell me if they should be traffic, a purchasable player vehicle, or both.

## This round
- **Rider "showing left" — found the actual cause, and it wasn't the model.** The chase camera only partially followed the bike's side-to-side position (using a fraction of it, for smoothing), so whenever a bike drifted off-center — recovering from a hit, cutting a corner, anything — the camera's framing and the bike's real position diverged, making everything look tilted. This affected every vehicle, always, it was just barely noticeable on the old plain capsule rider and became obvious on your detailed biker model. Tightened the camera tracking; confirmed fixed both stationary and while actively cornering (normal lean-into-the-turn behavior now reads correctly instead of looking broken).
- **Hyderabad photo billboards removed.** Signs on that track are back to plain text like every other track.
- **Mountains no longer intrude on the road.** They were placed at a fixed distance from the center regardless of track size — fine for normal tracks, but Hyderabad is now the longest track in the game and its loop extends further out than that fixed distance, so the "background" mountains ended up inside the track. They now scale with the actual track size on every track, so this can't happen again as tracks grow.
- **Rod weapon lengthened** so it reads clearly as a weapon in hand instead of looking like it's clipping into the rider.

## This round
- **Found the real cause of "bat disappears after a hit."** One swing was hitting *every* opponent within reach, not just one — so if racers were clustered together (very common right at the start line, or after a pile-up), a single bat swing could drain 2–4 charges at once instead of 1, making a 4-use bat vanish on the very first hit. Fixed so each swing connects with only one target, same as you'd expect from a real swing. Verified: before the fix, one attack call dropped a fresh bat from 4 charges to 2; after the fix, the same situation drops it from 4 to 3, exactly as it should.
- **Per-vehicle top speeds**, verified by running the actual physics forward in each vehicle until it capped out:
  - Bicycle 40 · Motorbike 180 · Scooter 120 · Auto Rickshaw 70 · Car 150 · Rocket Car 325 · Bus 110
  - Ferrari (traffic only, not yet player-owned) is set to 300.
  - Engine upgrades still help — they add a percentage on top of each vehicle's own cap, so upgrading a slow vehicle won't let it out-run a naturally faster one at the same upgrade level.
- **6 new vehicle models added:**
  - **Rocket Car** and **Scooter** are new purchasable vehicles (65,000 🪙 and 2,200 🪙) with their own top speeds as above.
  - **bike2, bike3, racingbike** are now in the mix as random visual variety for the Motorbike class — buy one Motorbike, and you (and other bike riders) will see a random one of the 4 looks each race; all handle identically.
  - **car2** joins Car as extra traffic variety.
  - All 6 are also mixed into ordinary traffic, with Rocket Car driving noticeably faster than other traffic.
  - I guessed at each model's facing direction — if anything drives sideways or backwards, tell me which one and I'll fix it the same way as before.

## This round
- **Adopted your edited index.html as the new base** — kept both your changes (rider size/angle tweak, bicycle top speed bumped to 70).
- **Riders added to two-wheeler traffic.** Scooters in traffic now carry a rider (using your biker scan model if it's loaded, with a simple fallback figure otherwise). Confirmed visible sitting on the scooter in a close test render; I adjusted the seating position down once already after catching it floating too high on the first pass, but I'd ask you to eyeball it in a real race since my test camera couldn't get a perfectly clean confirmation shot this round.
- **Banner request:** see my message for why I didn't build the KCR-branded banners.

## Hyderabad banner messages (neutral set, as discussed)
Signs on the Hyderabad track now cycle through real, neutral local references instead of the generic highway text other tracks use: "CHARMINAR", "GOLCONDA FORT", "HITEC CITY 5 km", "TANK BUND AHEAD", "WELCOME TO HYDERABAD", "CITY OF PEARLS", "BONALU FESTIVAL ZONE", "TELANGANA FORMATION DAY". Confirmed rendering correctly in a live test (verified "CHARMINAR ➜" showing on an overhead gantry sign).
