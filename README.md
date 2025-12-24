# 🎵 BEAT - Semantic Raw Format (SRF) Standard

<br />

## Quick Overview

BEAT is a unified protocol project where writing and reading events coexist on the same timeline, with consistent interpretation across diverse domains and platforms (Edge, xPU, Embedded, WebAssembly, etc.).

Traditional data formats separate `Event → Record → Analyze` sequentially, introducing latency. In BEAT, `Event ~ Record ~ Analyze` flows as one. This is achieved through its Semantic Raw Format (SRF) structure, where a 1-byte scan eliminates the need for separate parsing or transformation. Additionally, it expresses human decision flow (5W1H) as a linear stream, so humans and AI can read it together like natural language. This goes beyond simple performance optimization. It enables feedback loops where AI can interpret its own events immediately upon recording and refine decisions in real time. These characteristics also serve as exploratory research toward AGI and quantum computing.

The JSON example below is not meant to claim superiority over other formats, but to illustrate BEAT's structural characteristics. It reaches compression near the structural limit while preserving the causal story (Semantic) and event visibility that can be harder to follow in traditional formats. BEAT is designed to coexist with and respect the value of standard formats like JSON.

Do not interpret this comparison as mere data compression. BEAT's value is defined in the Specification section.

### JSON (Traditional Format)

**1,414 Bytes (Minified)**

`{"meta":{"device":"mobile","referrer":"search","session_metrics":{"total_scrolls":56,"total_clicks":15,"total_duration_ms":1205200}},"events_stream":[{"tab_id":1,"context":"home","timestamp_offset_ms":0,"actions":[{"name":"nav-2","time_since_last_action_ms":23700},{"name":"nav-3","time_since_last_action_ms":190800},{"name":"help","time_since_last_action_ms":37500,"repeats":{"count":1,"intervals_ms":[12300]}},{"name":"more-1","time_since_last_action_ms":112800}]},{"tab_id":1,"context":"prod","time_since_last_context_ms":4300,"actions":[{"name":"button-12","time_since_last_action_ms":103400},{"name":"p1","time_since_last_action_ms":105000,"event_type":"tab_switch","target_tab_id":2}]},{"tab_id":2,"context":"p1","timestamp_offset_ms":0,"actions":[{"name":"img-1","time_since_last_action_ms":240300},{"name":"buy-1","time_since_last_action_ms":119400},{"name":"buy-1-up","time_since_last_action_ms":2900,"flow_intervals_ms":[1300,800,800],"flow_clicks":3},{"name":"review","time_since_last_action_ms":53200}]},{"tab_id":2,"context":"review","time_since_last_context_ms":14000,"actions":[{"name":"nav-1","time_since_last_action_ms":192300,"event_type":"tab_switch","target_tab_id":1}]},{"tab_id":1,"context":"prod","time_since_last_context_ms":0,"actions":[{"name":"mycart","time_since_last_action_ms":5400,"event_type":"tab_switch","target_tab_id":3}]},{"tab_id":3,"context":"cart","timestamp_offset_ms":0}]}`

### BEAT (Semantic Raw Format)

**258 Bytes**

`_device:mobile_referrer:search_scrolls:56_clicks:15_duration:12052_beat:!home~237*nav-2~1908*nav-3~375/123*help~1128*more-1~43!prod~1034*button-12~1050*p1@---2!p1~2403*img-1~1194*buy-1~13/8/8*buy-1-up~532*review~140!review~1923*nav-1@---1~54*mycart@---3!cart`

<br />

## Specification

### BEAT Notation

**BEAT (Behavioral Event Analytics Transcript)** is an expressive format for multi-dimensional event data, including the space where events occur, the time when events occur, and the depth of each event as linear sequences. These sequences express meaning without parsing (Semantic), preserve information in their original state (Raw), and maintain a fully organized structure (Format). Therefore, BEAT is the Semantic Raw Format (SRF) standard.

BEAT expresses a 5W1H semantic stream using customizable token assignments within Printable ASCII (0x20 to 0x7E). BEAT is domain-agnostic and can be applied to Finance, Game, Healthcare, IoT, Logistics, and other environments, and each domain may freely adjust token assignments while maintaining this semantic stream. Other language, platform, or architecture implementations can be placed or linked in the BEAT repository’s `/reference` directory.

**`!` = Contextual Space (who)**

**`~` = Time (when)**

**`^` = Position (where)**

**`*` = Action (what)**

**`/` = Flow (how)**

**`:` = Causal Value (why)**

BEAT achieves binary-level (1-byte scan) performance while preserving the human readability of a text sequence. BEAT defines six core tokens within an eight-state (3-bit) semantic layout. Aligned with 5W1H, they fully capture the intent of human-designed architectures while leaving two states for domain-specific extensions. Together, they form the core notation of the BEAT format.

The underscore (_) is one example of an extension token used for serialization and to express meta fields, such as `_device:mobile_referrer:search_beat:!page~10*button:small~15*menu`. These meta fields annotate BEAT sequences without altering their core format while preserving 1-byte scan performance.

In Web domains such as Full Score, the at sign (@) is used as an extension token. It links multiple BEAT sequences, including cross-tab transitions. Extension tokens can be used in a similar way for app instances, device clusters, or any other parallel sequences in other domains.

Hyphens (-) and spaces ( ) are generally not treated as extension states and can be used freely as flexible bind markers.

### BEAT Generation

The eight-state semantic layout defines a structural constraint of the BEAT specification. To maximize expressive power, BEAT is designed so that all 2^3 states can be used. As a result, the semantic stream can be scanned efficiently across both classical and quantum computing environments.

The 3-bit (2^3) state layout is simply a design chosen in light of various trade-offs, and an implementation that uses BEAT, such as Full Score, can, for example, choose a Power Mode (POW) that uses only a 2-bit subset of the eight states, gaining simpler immediate batching at the cost of accepting fragmented streams. Conversely, expanding BEAT’s states to 4 bits increases expressive power, but also carries the possibility that constraints become looser, the format becomes unwieldy, and it becomes harder to maintain semantic discipline. Accordingly, BEAT will continue to evolve through ongoing research and refinement aimed at reducing these trade-offs, even when the number of states is reduced or increased.

BEAT's semantic stream remains substantially similar regardless of token assignment or how data is stored or transmitted, provided implementations follow BEAT's eight-state semantic layout. See the Compatibility criteria in the License section.

The examples below show one possible style. Constant definitions and code conventions may vary.

**Edge platform example**
```javascript
const S = '!';	// Contextual Space (who)
const T = '~';	// Time (when)
const P = '^';	// Position (where)
const A = '*';	// Action (what)
const F = '/';	// Flow (how)
const V = ':';	// Causal Value (why)
```

**xPU platform example**
```python
s = srf == 33			# '!' Contextual Space (who)
t = srf == 126			# '~' Time (when)
p = srf == 94			# '^' Position (where)
a = srf == 42			# '*' Action (what)
f = srf == 47			# '/' Flow (how)
v = srf == 58			# ':' Causal Value (why)
```

**Embedded platform example**
```c
#define SRF_S '!'				// Contextual Space (who)
#define SRF_T '~'				// Time (when)
#define SRF_P '^'				// Position (where)
#define SRF_A '*'				// Action (what)
#define SRF_F '/'				// Flow (how)
#define SRF_V ':'				// Causal Value (why)
```

**WebAssembly platform example**
```wat
(i32.eq (local.get $srf) (i32.const 33))	;; '!' Contextual Space (who)
(i32.eq (local.get $srf) (i32.const 126))	;; '~' Time (when)
(i32.eq (local.get $srf) (i32.const 94))	;; '^' Position (where)
(i32.eq (local.get $srf) (i32.const 42))	;; '*' Action (what)
(i32.eq (local.get $srf) (i32.const 47))	;; '/' Flow (how)
(i32.eq (local.get $srf) (i32.const 58))	;; ':' Causal Value (why)
```

This example illustrates how a BEAT sequence such as `!military~10^3000*training~10/15/10/20/10/15*study~200*medical-licensing-exam:pass~100!hospital~10*consultation` can flow across layers without translation and be handled with a 1-byte scan. For practical examples of how BEAT can be used in real architectures, see the README and reference implementations in the `/reference` directory.

As the Semantic Raw Format (SRF) standard, BEAT removes most of the traditional parsing pipeline. Handling only needs address arithmetic to load and store tokens. In short, it achieves binary-level performance while preserving the human readability of a text sequence.

The result is as follows.

```
Traditional Parsing: Bytes → Tokenization → Parsing → Tree Construction (Memory) → Field Mapping (CPU) → Value Extraction → Handling
⛔ 7 Steps, μs to ms-level overhead (varies by payload)

BEAT: Bytes ~ 1-byte scan → Handling
✅ 2 Steps, μs-level overhead

# No Tokenization
# No Parsing
# No Tree & Object Allocation
```

### BEAT Architecture

Traditional data formats, including JSON, are like dots. They're great for organizing and separating individual events, but understanding what story they tell requires parsing and interpretation.

BEAT is like a line. It captures the same data as JSON, but because the user journey flows like music, the story becomes clear right away.

BEAT expresses its semantic states using only Printable ASCII (0x20 to 0x7E) tokens that pass smoothly through compute and security layers. No separate encoding or decoding is required, and because it's small enough to live in native storage, real-time analysis runs without delay across most environments.

So BEAT is raw data, but it's also self-contained. No semantic parsing needed. This sounds grand, but it's really not. The BEAT expressive format is inspired by the most common data format in the world. The oldest data format in human history. Natural language.

And AI is the expert at understanding natural language.

```
_device:1_referrer:5_scrolls:32_clicks:8_duration:12047_beat:!home~237*nav-2~1908*nav-3~375/123*help~1128*more-1~43!prod~1034*button-12~1050*p1@---2~54*mycart@---3
_device:1_referrer:1_scrolls:24_clicks:7_duration:11993_beat:!p1~2403*img-1~1194*buy-1~13/8/8*buy-1-up~532*review~14!review~1923*nav-1@---1
_device:1_referrer:1_scrolls:0_clicks:0_duration:12052_beat:!cart
```

Multiple BEAT sequences can be written in an NDJSON-compatible line format, with each journey kept on a single line. This keeps logs compact, makes querying simple, and improves AI analysis efficiency. Across Finance, Game, Healthcare, IoT, Logistics, and other environments, BEAT’s semantically complete stream allows fast merging and easy compatibility with their respective formats.

Of course, this NDJSON-style representation is optional. The same data can be expressed in a simplified BEAT format while preserving its 1-byte scan performance, such as: `_🔎scrolls:🔎56_🔎clicks:🔎15_🔎duration:🔎1205.2_🔎beat:🔎...`. Here, the 🔎 emoji highlights positions immediately after each 1-byte scan token.

The purpose of this representation is to respect traditional data formats, including JSON, and the services built around them (such as BigQuery), so that BEAT can be adopted easily and coexist with them rather than trying to replace them.

```
{"device":1,"referrer":5,"scrolls":56,"clicks":15,"duration":1205.2,"beat":"!home ~23.7 *nav-2 ~190.8 *nav-3 ~37.5/12.3 *help ~112.8 *more-1 ~4.3 !prod ~103.4 *button-12 ~105.0 *p1 @---2 !p1 ~240.3 *img-1 ~119.4 *buy-1 ~1.3/0.8/0.8 *buy-1-up ~53.2 *review ~14 !review ~192.3 *nav-1 @---1 ~5.4 *mycart @---3 !cart"}

AI Insights:
[CONTEXT] Mobile user, Mapped(5) visit, 56 scrolls, 15 clicks, 1205.2 seconds
[SUMMARY] Confused behavior. Landed on homepage, hesitated in help section with repeated clicks at 37 and 12 second intervals. Moved to product page, opened details in a new tab, viewed images for about 240 seconds. Tapped buy button three times at 1.3, 0.8, and 0.8 second intervals. Returned to the first tab and opened cart shortly after, but didn’t proceed to checkout.
[ISSUE] Cart reached but purchase not completed. Repeated buy actions may reflect either intentional multi-item additions or friction in option selection. Long delay before checkout suggests uncertainty.
[ACTION] Evaluate if repeated buy or cart actions represent deliberate comparison behavior or checkout friction. If friction is likely, simplify option handling and highlight key product details earlier in the flow.
```

Humans learn the meaning of their actions as they acquire language. AI, by contrast, excels at generating language but struggles to autonomously structure and interpret the full contextual fabric (5W1H) of its own actions. With BEAT, AI can record its behavior as sequences that read like natural language and analyze that flow in real time (1-byte scan), providing the foundation for feedback loops through which it can monitor its own errors and improve its outcomes.

Writing and reading coexist on the same timeline. Scaling computation alone may not lead to AGI. These feedback loops can serve as nerves for AI, where `Event ~ Record ~ Analyze` flows as one.

Beyond classical computing, BEAT's 3-bit semantic layout maps naturally to a 3-qubit state space. Its parsing-free linear stream is well-suited for quantum computing environments.

<br />

## Consistent Interpretation

BEAT is a data format because it defines a linear event stream using semantic states. It is also a protocol because those same states enable consistent interpretation across diverse domains and platforms, while supporting real-time streaming where readers can scan bytes as they arrive.

Semantic Raw Format (SRF) expresses this dual nature. BEAT is the SRF standard.

<br />

## License

- **BEAT**: GPL-3.0-or-later License - [https://github.com/aidgncom/beat](https://github.com/aidgncom/beat)
- **Resonator**: AGPL-3.0-or-later License - [https://github.com/aidgncom/resonator](https://github.com/aidgncom/resonator)

BEAT is the Semantic Raw Format (SRF) standard. BEAT sequences can be directly read by both humans and AI without parsing (Semantic), preserve information in their original state (Raw), and maintain a fully organized structure (Format). Distributing BEAT or modifications to this format specification must comply with GPL-3.0-or-later terms.

Resonator is the official reference implementation for interpreting BEAT and is licensed under the AGPL-3.0-or-later. This license applies across Finance, Game, Healthcare, IoT, Logistics, and other environments, and internal use is unrestricted. If BEAT-based logic, as defined and implemented, is provided to third parties, the AGPL-3.0-or-later source disclosure requirements apply.

BEAT is defined as an expressive format for structured semantics. Consistent interpretation of BEAT across different environments is also important to maintain semantic compatibility. Therefore, alternative implementations that claim equivalence are expected to use the Compatibility criteria provided below to verify interpretation consistency.

**Compatibility**: BEAT is considered compatible even if the tokens vary within Printable ASCII (0x20 to 0x7E) or the implementation differs, as long as event data is expressed using the sequential notation defined in the BEAT specification, preserves expressive semantics including but not limited to the space where events occur, the time when events occur, and the depth of each event, and maintains a substantially similar semantic stream regardless of how it is stored or transmitted. Semantic compatibility is determined by the semantic stream expressed within BEAT's eight-state (3-bit) semantic layout, irrespective of implementation details such as token choice, token order, token subsets, or storage representation. Any such compatible implementation constitutes a derivative work under copyright law and must comply with GPL-3.0-or-later terms.

See individual source files for detailed license information.

<br />

## Resources

If you would like to get in touch, feel free to reach out via email or DM on X. Thank you.

- **Email**: [info@aidgn.com](mailto:info@aidgn.com)
- **X**: [https://x.com/aidgncom](https://x.com/aidgncom)
- **YouTube**: [https://www.youtube.com/@aidgn](https://www.youtube.com/@aidgn)

<br />
