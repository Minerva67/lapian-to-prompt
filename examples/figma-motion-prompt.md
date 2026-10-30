# 复原提示词：Figma "Introducing Figma Motion"（68s）

> 上游：`video-lapian` 产出的拉片报告（逐秒镜头表 + 十节分析）
> 本文件：`lapian-to-prompt` 的产出

## 路线判断

- **路线：codegen 为主，加少量 `[IMG]` 素材槽。** 全片画面主体是纯色底、编辑器 UI、几何插画、图标、时间轴和代码面板，这些用 HTML/CSS/SVG 都能精确还原，文字和参数可以做到逐字一致。需要照片的只有几处：骑行海报、沙漠充气穹顶、3D 卡片阵列里的几张产品/地貌照片、3D 礼花筒。这些标成 `[IMG]`，每个都带渐变或线稿的退化占位。
- **codegen 适配度：⭐⭐⭐（高）。** 属于 UI demo 型，但界面是"设计过的演示界面"而不是真实录屏，代码画出来反而更干净。
- **片型：⑧ SaaS demo 片为主，叠加 ③ 排版发布片的做法。** 光标当主角，按真实工作流逐个讲功能；每个功能落回一张高完成度的作品。原片没有 SaaS demo 片常见的"痛点问句开场"，改用"成品先行"开场，复原时照原片来，不补问句。
- **画幅：16:9 横屏 1920×1080**（官方 YouTube 产品发布片）。
- **招牌机制：动效即演示 + "推近控件 → 拉远看成品"循环。** 每个功能先放大到一个控件，光标操作，再连续拉远，揭示它其实是一张精致作品的一部分。第三幕里，运动路径点的疏密随贝塞尔曲线实时变化，这个细节必须由真实的缓动函数驱动。

## 复原 prompt

```
Build a single self-contained, playable web animation that faithfully recreates Figma's
68-second launch film "Introducing Figma Motion: Your canvas now has a timeline". Output ONE
index.html with all CSS/JS inline (GSAP via CDN allowed for the master timeline; otherwise
Web Animations API). Autoplay, loop, and expose play/pause + a scrub slider.

SIGNATURE MECHANIC — "the demo IS the motion": every feature is shown by zooming INTO a
single control, letting a cursor operate it, then pulling the camera OUT in one continuous
move to reveal that the control is part of a polished, finished design. Repeat this
zoom-in → operate → pull-out loop for every feature. Inside the easing beat, the dotted
motion path MUST be computed from the actual cubic-bezier currently shown in the curve
editor, so dot spacing visibly changes when the curve changes. Never replace these moves
with generic fades.

=== GLOBAL DESIGN SYSTEM ===
- Stage: 1920×1080 (16:9), letterboxed and centered in any viewport.
- Total duration 68.0s, driven by ONE master timeline in ms. All beats below use absolute
  timecodes. Honor them.
- Each feature lives in its own flat, full-bleed background color. Changing feature =
  hard cut to a new background. Black is reserved for: title card, dev handoff, outro.
- Palette:
  · Brand black #000000 — title, Dev Mode, outro
  · Title orange-red #F0673C — "Figma Motion" and the closing line (same color, bookend)
  · Forest #19301E (web page bg), deep green #126B03 (icons), pale lime #E8FFC2 (tokens bg),
    bright lime accent ~#D8F55A
  · Electric violet #4900C8 (bg), lavender #CBB7FC (toggle track), pale lilac #EBE2FC
    (inverted "done" bg), deep indigo ~#2A0A8C (filled toggle)
  · Map grey #CBD1D3, warm orange #F38F30, charcoal #252525, slate blue #7589A2,
    canvas grey #565656
  · Interface blue ~#0D99FF — selection boxes, handles, playhead, keyframe diamonds,
    timeline segments, AI button. This blue is the only constant accent across scenes.
- Type: title/tagline in a geometric grotesk (e.g. "Inter Display", "Figtree", system-ui),
  ~180px, tight leading. UI labels in Inter 13–16px (scaled up 2–3× when the camera zooms
  in). Toggle word "Unlocked" in a bold condensed sans ("Oswald", "Barlow Condensed").
  Web-page headings in a serif ("Fraunces", Georgia). Poster type "CANYON CREW MEETUP" in
  a dot-matrix/pixel display face, or draw it as an SVG grid of rounded cells.
- Cursor = the only actor. Build ONE reusable Cursor component with two skins: black arrow
  with white outline, and open-hand "grab". It moves along eased arcs (never linear), with
  a small press-scale (0.9) on click. Collaborator cursors reuse it, recolored blue/green
  and labeled.
- Default entrance for icons/cards: scale 0.6→1 with a slight overshoot spring (~320ms).
- Camera = a single wrapper transform (scale + translate) per scene. Pull-outs are one
  continuous ease-in-out move (600–1200ms), never a cut.
- Pacing: a visual change roughly every 1.2–1.4s. Hard cuts only between features.
  Planned holds: title card ~3s; final logo ~1s.

=== ASSET SOURCING ===
Pure code (SVG/CSS): all backgrounds, the editor chrome, timeline panels, toggle, curve
editor, isometric map, icon grid, bento tiles, phone UI, code panel, collage poster cards,
geometric outro shapes and the logo assembly. A few beats need imagery, marked [IMG: ...].
Source each however the build allows (generate, or a suitable royalty-free photo/render)
and composite it in. Rules:
- PHONE SCREENS: draw a rounded-rect phone frame in code and render all UI inside it with
  code. Never bake screen contents into a still image.
- Fake every camera move and every 3D tilt with CSS transforms on the image (perspective,
  rotateX/rotateY, translateZ).
- Every [IMG] MUST degrade to a tasteful fallback (gradient + simple line art in the scene
  palette), never a grey box. The film must play end-to-end with zero external assets.

=== TIMELINE (absolute timecodes) ===

ACT 1 — Finished work first, then pull out to reveal the editor (0:00–0:07.0)
0:00.0  Flat warm grey #D8D9D2 for ~0.5s.
0:00.5–0:02.8  Extreme close-up, heavily blurred (CSS blur 40px→0): a white rounded blob on a
        dark-green stem over bright lime; drift and scale up slowly. Viewer should not yet
        know what it is.
0:03.0  Focus snaps sharp: it is one illustrated card — white circles forming a dandelion
        with a dark-green asterisk stem — neighboring cards peek in at the edges.
0:03.5  MUSIC DROP (full beat comes in).
0:03.5–0:04.2  Pull out to a full web page on forest bg #19301E. Top-left wordmark
        "Modern Garden"; nav right: "Explore  Guides  Collections  Shop" (Guides underlined).
        Centered serif heading "The Latest". Three cards with geometric flower art and
        captions: "12 min" / "Plant Care 101", "15 min" / "Light Guide", "8 min" /
        "Seed Starting", each with a small ↗ chip. The flowers are subtly animating.
0:04.2–0:06.8  Keep pulling out in the same move: the page becomes a frame inside a design
        editor floating on a lime noise-texture background. Left panel: layers "Flower 1",
        "Flower 2", "Flower 3". Right panel: property inspector. Bottom: a NEW timeline
        panel with a ruler, a blue playhead sweeping right, blue bars labeled "Rotation",
        a "Position" track, and a lavender track "Leaf 14". As the playhead moves, the
        third card's illustration morphs (round seed → tulip shape), synced to the playhead.

ACT 2 — Title card (0:07.0–0:10.0)
0:07.0  HARD CUT to black.
0:07.0–0:07.75  "Figma" appears, then "Motion" reveals left-to-right (mask wipe), stacked,
        orange-red #F0673C, ~180px, centered-left. Around it, collage tiles pop in with the
        spring entrance: poster cards "SPACESHIP EARTH", "RE: FURNISHED", "THE QUIET
        SPECTRUM" (bold black condensed type on a pale label, partly cropped by the frame);
        [IMG: translucent inflatable pod structure on moss, with a lime node-graph overlay];
        a halftone dot-pattern leaf in lime on dark green; a pixel-block tile of green/
        violet/orange squares.
0:07.75–0:10.0  HOLD the title (static). Only the surroundings move: poster tiles swap,
        pixel blocks rearrange every ~0.6s, tiles drift slowly.

ACT 3 — Keyframes + easing (0:10.0–0:22.0)
0:10.0  HARD CUT: electric violet #4900C8. A giant lavender pill (toggle track) fills the
        frame (camera very close).
0:10.25–0:10.75  A deep-indigo round knob scales in with a blue rotate-selection box; the
        GRAB cursor drags it, rotating it ~30°.
0:11.2  Camera eases back slightly; a timeline ruler fades in at the bottom:
        "0  0.2  0.4  0.6  0.8  1  1.2" with a blue playhead.
0:12.0–0:12.8  Axis gizmo on the knob: green up-arrow and pink right-arrow handles. Arrow
        cursor grabs the pink arrow.
0:13.0–0:13.5  Cursor drags the knob to the right end of the track. A trail of evenly
        spaced blue dots + a white keyframe diamond at the start is left behind. A blue
        segment bar 0→0.6 appears on the timeline.
0:14.2  Camera pushes into the timeline: blue segment with an easing handle (diagonal icon)
        in the middle; ruler "0  0.2  0.4  0.6".
0:15.0  Cursor clicks the easing handle.
0:15.8  Curve editor pops up on the right: dropdown "Linear", tabs "Curve | Spring" (Curve
        active), a straight diagonal curve, value field "0, 0, 1, 1". Motion-path dots on
        the left are evenly spaced.
0:16.5–0:17.2  Cursor drags the curve handle; value field reads "0, 0, 0.3, 1" (ease-out).
        Dots re-space live: tighter near the end.
0:17.5–0:18.0  AUDIO DROPOUT (music ducks ~10dB for half a beat).
0:18.0–0:18.8  Cursor keeps dragging into an S-curve; value field reads "0.67, 0, 0.3, 1".
        Dots now bunch at both ends, sparse in the middle.
        The value field and dot spacing MUST derive from the same bezier function.
0:19.0–0:19.4  Panel closes, toggle shrinks to normal size; knob (four-point star icon)
        slides right using exactly the last curve.
0:19.5  ON THE BEAT: background inverts to pale lilac #EBE2FC; the track fills deep indigo;
        bold condensed pale-lavender "Unlocked" appears inside the pill.
0:20.2–0:21.8  Pull out: the toggle is the bottom component of a phone screen on violet bg.
        Screen: heading "Goal Reached" (bold condensed, indigo), [IMG: chrome 3D party
        popper bursting silver streamers over a lavender blob], text "You saved $200 for
        Headphones", toggle "Unlocked". The phone card pans right → left → center.

ACT 4 — Animation styles (0:22.0–0:26.6)
0:22.0  HARD CUT: map grey #CBD1D3. Isometric line-drawn floor plan (black 3px rounded
        paths, gaps for doors). Lime circular markers "C", "D", "A", "H", "B", "E" shown
        multi-selected (blue boxes); one orange marker "F". Camera pans diagonally.
0:23.2  Cursor moves onto "F".
0:24.0  Panel "Animation styles" with "+" and "×"; rows with icons: "Position", "Scale",
        "Rotation", "Opacity".
0:24.8  Panel fades; markers start animating in.
0:25.5–0:26.5  Pull out: the map sits inside an exhibition web page — left column with a
        bold headline highlighted in lime (cropped by the frame: "…MATION", "…hibition",
        "…HRESHOLDS"), [IMG: aerial photo of an orange geodesic dome in green countryside];
        right: the map with a floor switcher "3 / 2 / 1" (2 active). Markers turn into
        standing pins and drop into place A–H with a small stagger.

ACT 5 — Prompt the agent (0:26.75–0:36.5)
0:26.75  HARD CUT: warm orange #F38F30. Phone (status bar "9:41"), dark event app. Hero
        image selected with blue handles: [IMG: black-and-white motion-blurred cyclist on a
        trail] with dot-matrix title "CANYON CREW MEETUP" (two lines). Below: "Trail Crew
        Meetup", "TOPANGA, CA", "35 · 5:45PM - 7:45PM", date chip "JUN" / "28". A blue
        four-point-star AI button sits next to the selection.
0:28.5  Cursor clicks the AI button.
0:29.2  Push in; background goes charcoal #252525. A white prompt box types character by
        character (~28 chars/s): "Create a glitchy type animation, staggering the shapes."
        A "+" bottom-left, blue round send button bottom-right.
0:30.8  Cursor clicks send.
0:31.5–0:32.5  Generating: a light sweep shimmers across the image; the AI button spins.
0:33.0  AUDIO DROPOUT (short). Blue check badge appears. The dot-matrix letters break into
        horizontal dash fragments.
0:33.8–0:36.5  A timeline slides up (ruler "0 … 1.6") showing several keyframe segments
        offset in a stagger. The glyph cells jitter, shift sideways, and re-assemble into
        "CANYON CREW MEETUP" group by group, following the stagger order. Cursor drags one
        segment. The glitch must be a real per-cell code animation, not a filter wobble.

ACT 6 — 3D transform (0:36.75–0:44.0)
0:36.75  HARD CUT: slate blue #7589A2. An image card swings in from bottom-left with a 3D
        rotateY/rotateX entrance.
0:37.5  Card settles: [IMG: white inflatable dome in a desert, tiny figure at the entrance].
        Dark tooltip: "Transform effects" / "3D transform".
0:38.2  Panel "3D transform" (move + close icons) with a square pad and a center dot.
0:39.0–0:39.8  GRAB cursor drags inside the pad; the photo tilts in perspective in real
        time; the pad shows a small cube preview.
0:40.5–0:42.0  More cards fan out into a deep 3D stack (perspective 1400px): [IMG: green
        puffer jacket], [IMG: red-earth landscape from above], [IMG: white chunky sneaker],
        an orange-to-red gradient card (code), [IMG: translucent inflatable pod],
        [IMG: orange geodesic dome]. Nav pills fade in on top: "What's On", "Art & Artists",
        "Visit", "Shop", "Tickets". A lime square tile with a node-star glyph sits center.
0:42.8–0:43.5  The tile expands into the wordmark "M✻SF" (middle letter is the node-star
        glyph), black on lime. Camera pulls out to show it as a web frame on canvas grey
        #565656. A round collaborator avatar pops in bottom-right.

ACT 7 — Collaboration (0:44.0–0:50.4)
0:44.2  Comment card: header "Comment" with "•••", ✓ and ×; avatar + "Ava" + "6 hours ago";
        body: a timecode chip "0.4s" then "Let's speed up this part of the animation!";
        reply row with a yellow "A" avatar and placeholder "Reply". (Names are placeholders.)
0:45.8  Cursor clicks the "0.4s" chip (turns solid blue).
0:46.5  Jump to the timeline: length "3000 ms", ruler "0  200  …  600  …  1000  1200",
        playhead snaps to 400ms. Two avatar pins ride on the ruler. Tracks: "Card 01" with
        "Position" and "Opacity" rows, "100%".
0:47.2  Cursor drags the "Position" segment shorter (the speed-up).
0:48.0–0:50.4  Back to the 3D stack. Blue cursor labeled "Ava" top-left; a green cursor
        labeled "Leo" enters right. Leo's chat bubble types "Looking good!". Meanwhile the
        center tile replays: dot glyph → node-star → "M✻SF".

ACT 8 — Motion tokens (0:50.5–0:56.75)
0:50.5  HARD CUT: pale lime #E8FFC2. A large deep-green chat-bubble icon scales in; the
        camera pulls back while other icons spring in one by one (stagger ~80ms) into a 3×3
        grid: gear, user, search, chat, home, bell (lime dot), wallet, trend line, archive
        box with down arrow. Deep green #126B03 with bright-lime inner details.
0:51.8  Panel "Easing" (+, ×): dropdown "Brand Motion"; section "Motion Tokens"; rows
        "Brand: Linear", "Brand: Snappy".
0:52.5  A third row slides in: "Brand: Playful". Cursor hovers "Brand: Snappy" (icons
        replay crisp and fast).
0:53.2–0:54.0  Cursor selects "Brand: Playful" (row highlights light blue). Icons replay
        with a wobbly overshoot: search lens tilts, wallet flips up, bell swings.
        Each token = a named easing preset applied to the same icon loop.
0:54.8–0:56.25  Pull out into a bento grid of brand tiles (pale lime / deep green / lime):
        the icon grid; badge list "Finance Nerd" / "Double Down" / "Stack Master" with
        progress bars; buttons "Transfer", "Watchlist", "Wallet" + a round "×"; wordmark
        "Cheddar" (the C drawn as a toggle); balance card "Total savings" counting up to
        "$194.70" with "Deposit" / "Transfer"; spending bars "$212" (lime) "$56" (cyan)
        "$29" (pink) "$16" (violet) with legend "Trips • Entertainment • Food • Clothes";
        a green blob tile with the party popper [IMG, reuse].
0:56.25–0:56.75  Tiles fly outward off-frame.

ACT 9 — Dev handoff (0:57.0–1:02.6)
0:57.0  Black. Centered phone screen: "Hi, Jamie"; balance counts "$20.50" → "$194.70"
        (rolling digits); "Deposit" / "Transfer"; "Recent spending" bars; "Goals".
0:58.5–0:59.2  Screen scrolls: "Headphones $280.00", "Sneakers $120.00", "Freshman Trip
        $500.00"; a "New badge!" card; "Completed goals": Skateboard, Camera, Art Book.
1:00.0  Profile screen: badge icon, "Jamie K.", "@jamieh", buttons "Edit" / "Share",
        "Savings Streak" with days "S M T W T F S" (some checked), "Badges" → "Finance Nerd"
        "8 of 10 articles read".
1:00.8  A dark Dev Mode panel slides in from the right: header "Motion", segmented
        "List | Code" (Code active), language dropdown "CSS"; section "Animation"; "Style"
        line 1: `animation: rotate_1 2s;`; "Keyframes" empty. Streak checks fill in one by
        one on the left while the badge icon rotates.
1:01.5  Keyframes block types in, syntax-highlighted:
        @keyframes rotate_1 {
          0%   { transform: rotate(0deg); }
          100% { transform: rotate(360deg); }
        }
1:02.2–1:02.6  Cursor hovers the copy icon → tooltip "Copy Keyframes"; the code block
        flashes green-highlighted. "Assets" section header visible at the bottom.

ACT 10 — Recap, line, logo (1:02.75–1:08.0)
1:02.75  HARD CUT to black. Mini versions of earlier finished pieces float as a collage:
        the icon grid, the 3D card stack with "M✻SF", the violet party-popper card. A lime
        rotation dial tile reads "8°" with a blue slider; the cursor drags it.
1:03.25–1:04.5  "Set your ideas" / "in motion" appears word by word in #F0673C (same as the
        title). The dial counts "66°" → "166°" → "353°" in sync with the drag. Collage drifts.
1:04.5  Music steps down (about 6dB).
1:05.2  Collage clears. Five flat shapes remain, each with small square selection handles
        (violet or lime): an orange circle, an olive circle, a green circle inside a
        pink/orange checkered square frame, a small cyan circle, a red-orange pill.
1:05.2–1:06.8  Shapes glide to center and snap together into the Figma logo (red, orange,
        purple, blue, green); the center blue circle still shows a selection ring.
1:06.8–1:08.0  Handles vanish; clean logo holds ~1s on black. End.

=== SOUND (optional, WebAudio) ===
Instrumental electronic bed, bass/kick driven, ~125 BPM, synthesized in WebAudio (no
copyrighted track). Quiet intro 0:00–0:03.5, full beat from 0:03.5. Two half-beat
dropouts: 0:17.5–0:18.0 (before "Unlocked") and 0:33.0–0:33.5 (AI done). Soft UI ticks on
cursor clicks, a short whoosh on each pull-out. Step down at 1:04.5, end on the logo.
No voiceover.

=== TECHNICAL REQUIREMENTS ===
- One master timeline; every beat keyed to the timecodes above (ms).
- Autoplay + loop; play/pause + scrubber bound to timeline progress (scrubbing must
  re-render deterministically, including typing, counters and the dot path).
- 1920×1080 stage scaled to the viewport; no horizontal scroll.
- Reusable components, built once: Cursor (arrow/grab, collaborator variants with label),
  SelectionBox (blue handles), TimelinePanel (ruler, playhead, segments, keyframe
  diamonds), FloatingPanel (title + rows), PhoneFrame, CameraRig (scene wrapper
  scale/translate for zoom-in and pull-out).
- Bezier utility shared by the curve editor, the motion-path dot spacing, and the knob
  slide, so all three always agree. Value fields show exactly "0, 0, 1, 1",
  "0, 0, 0.3, 1", "0.67, 0, 0.3, 1".
- Typing effects and number counters (balance, dial degrees) are code-driven and scrub-safe.
- Prefer SVG + CSS transforms; GPU-friendly (transform/opacity only for motion).
- Clean, commented code. Every [IMG] degrades to a palette gradient + line-art fallback.
```

## 使用提醒

| 项 | 说明 |
|---|---|
| 画幅 | 16:9 横屏 1920×1080，不要做成竖屏 |
| 代码 vs 素材 | 除 `[IMG]` 标注的照片和 3D 礼花筒外全部代码绘制；没有素材时整片也必须能播完 |
| 招牌机制别偷懒 | "推近控件 → 拉远看成品"必须是连续的相机运动；如果被做成硬切或淡入淡出，整片的说服力就没了 |
| 缓动一致性 | 面板数值、路径点疏密、旋钮滑动必须共用同一个贝塞尔函数，否则第三幕的核心细节会露馅 |
| 节奏陷阱 | 片名卡要静止保持约 3s，只有周边动；功能内部不要加硬切，只在功能之间硬切 |
| 空拍 | 0:17.5 与 0:33.0 两处音乐空拍决定了"完成"时刻的力度，有声音版本时不要省 |
| 文字逐字 | 曲线数值、"Animation styles" 四个选项、AI prompt 原句、CSS 代码均照原片，不要改写 |
| 人名 | 原片 UI 中的协作者真名已替换为占位名 "Ava" / "Leo"；"Jamie" 为原片示例用户名 |
| 品牌与商标 | Figma 名称与 logo 属于其所有者，此复原仅供学习；用于自己的产品时把片名、logo、示例内容换成自己的 |
| 音频 | 原片音乐未识别（拉片阶段只做了响度推断），这里用 WebAudio 合成替代，不使用原曲 |

串联用法：先用 `video-lapian` 把视频拆成拉片报告，再用 `lapian-to-prompt` 把报告转成这份复原提示词。
