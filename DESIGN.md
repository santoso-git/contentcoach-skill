---
name: ContentCoach skill homepage
description: The public page for the ContentCoach client skill, dressed as a photo lab's processing envelope.
colors:
  envelope-yellow: "#f7c21b"
  envelope-deep: "#e9ae00"
  printing-ink: "#141311"
  ink-soft: "#3d3a33"
  lab-red: "#d7261e"
  print-paper: "#fbfaf6"
  paper-shade: "#f1efe8"
  hairline: "rgba(20,19,17,.22)"
  muted: "#5d5a52"
  slip-text: "#f5f2e8"
  slip-comment: "#a8a293"
  envelope-bright: "#ffd54a"
typography:
  display:
    fontFamily: "Sofia Sans Extra Condensed, Sofia Sans, ui-sans-serif, system-ui, sans-serif"
    fontSize: "min(15.2vw, 228px)"
    fontWeight: 800
    lineHeight: 0.78
    letterSpacing: "-0.012em"
  headline:
    fontFamily: "Sofia Sans Extra Condensed, Sofia Sans, ui-sans-serif, system-ui, sans-serif"
    fontSize: "clamp(46px, 7vw, 92px)"
    fontWeight: 800
    lineHeight: 0.92
    letterSpacing: "-0.005em"
  title:
    fontFamily: "Sofia Sans Extra Condensed, Sofia Sans, ui-sans-serif, system-ui, sans-serif"
    fontSize: "30px"
    fontWeight: 800
    lineHeight: 0.92
    letterSpacing: "-0.005em"
  lede:
    fontFamily: "Sofia Sans, ui-sans-serif, system-ui, sans-serif"
    fontSize: "clamp(19px, 2.1vw, 24px)"
    fontWeight: 500
    lineHeight: 1.35
  body:
    fontFamily: "Sofia Sans, ui-sans-serif, system-ui, -apple-system, Segoe UI, sans-serif"
    fontSize: "17px"
    fontWeight: 400
    lineHeight: 1.55
  label:
    fontFamily: "Sofia Sans, ui-sans-serif, system-ui, sans-serif"
    fontSize: "12px"
    fontWeight: 700
    lineHeight: 1
    letterSpacing: "0.1em"
  mono-order:
    fontFamily: "JetBrains Mono, ui-monospace, SF Mono, Menlo, monospace"
    fontSize: "15px"
    fontWeight: 500
    lineHeight: 1.45
  mono-backprint:
    fontFamily: "JetBrains Mono, ui-monospace, SF Mono, Menlo, monospace"
    fontSize: "11.5px"
    fontWeight: 500
    lineHeight: 1.3
  mono-command:
    fontFamily: "JetBrains Mono, ui-monospace, SF Mono, Menlo, monospace"
    fontSize: "14px"
    fontWeight: 400
    lineHeight: 1.7
rounded:
  none: "0px"
spacing:
  gutter: "clamp(16px, 4vw, 48px)"
  container: "1180px"
  section: "clamp(64px, 9vw, 112px)"
  label-column: "92px"
  label-column-narrow: "64px"
  rule: "2px"
components:
  button-primary:
    backgroundColor: "{colors.printing-ink}"
    textColor: "{colors.envelope-yellow}"
    rounded: "{rounded.none}"
    padding: "16px 22px"
  button-primary-active:
    backgroundColor: "{colors.lab-red}"
    textColor: "{colors.print-paper}"
  button-copy:
    backgroundColor: "{colors.envelope-yellow}"
    textColor: "{colors.printing-ink}"
    rounded: "{rounded.none}"
    padding: "9px 12px"
  button-copy-hover:
    backgroundColor: "{colors.envelope-bright}"
  button-copy-done:
    backgroundColor: "{colors.lab-red}"
    textColor: "{colors.print-paper}"
  order-form-label:
    typography: "{typography.label}"
    width: "{spacing.label-column}"
    padding: "14px 12px"
  order-form-fill:
    typography: "{typography.mono-order}"
    padding: "10px 14px"
  tick-box:
    backgroundColor: "{colors.print-paper}"
    rounded: "{rounded.none}"
    size: "18px"
  print:
    backgroundColor: "{colors.print-paper}"
    rounded: "{rounded.none}"
    padding: "12px 12px 16px"
  command-slip:
    backgroundColor: "{colors.printing-ink}"
    textColor: "{colors.slip-text}"
    typography: "{typography.mono-command}"
    padding: "16px 18px"
  say-slip:
    backgroundColor: "{colors.print-paper}"
    textColor: "{colors.printing-ink}"
    padding: "14px 18px"
---

# Design System: ContentCoach skill homepage

**Scope.** This file describes the skill homepage in `docs/`, published by GitHub Pages at skill.contentcoach.se. The ContentCoach web app is a separate repository with its own, different system (near-neutral surfaces with one gold for the spending action, rounded panels and soft shadows). Nothing here describes or overrides it, and nothing in it constrains this page.

## Overview

**Creative North Star: "The Processing Envelope"**

The page is the paper packet a photo lab hands across the counter. Envelope stock in signal yellow owns whole bands of the page; one black printing ink does all the printing; a lab red is kept back for the pen that ticks a box. The visitor fills in an order (a sentence), ticks a size, hands it in at their counter (their coding agent), and prints come back with the lab's back-print beneath them: frame number, model, size, cost.

Everything is flat paper. Ruled fill-in tables with 2px ink rules, square tick boxes, a perforation row, docket numbers, a rubber date stamp. Depth comes only from sheets overlapping and from a sheet being one shade further back (the envelope's pocket and its back). Prints are white-bordered and slightly askew, as if laid loose on a counter. It deliberately refuses the dark dev-tool hero around a fake terminal: commands appear as printed slips inside the order, not as the page's frame.

Type is split the way a lab's paperwork is: a condensed grotesk for what is printed on the envelope, a plain sans for what a person reads, and a mono for what the lab's machines print (the typed order line, the back-print, the command slips).

**Key Characteristics:**
- Whole-band yellow envelope stock alternating with off-white print paper; a black footer closes the packet.
- One ink, 2px ruled tables, square corners everywhere.
- Lab red reserved for ticked, pressed and focused states.
- Depth by overlap and by a deeper shade of the same stock, never by shadow.
- Real prints with their back-print (model, size, cost) and prompt.

## Colors

A printed-matter palette: one paper stock, one envelope stock in two shades, one ink, one reserved red.

### Primary
- **Envelope Yellow** (envelope-yellow): the stock of the envelope. Owns full-width bands (the header and the drop-off section), the text colour of the primary button, the copy button, and the `theme-color`.
- **Envelope Deep** (envelope-deep): the same stock one sheet further back. The pocket the prints slide out of and the back of the envelope (the terms section) use it; the flap triangle above the back is drawn in Envelope Yellow.

### Secondary
- **Lab Red** (lab-red): the pen mark. Crossed strokes inside a ticked box, the underline under the ticked agent name, the pressed primary button, the copy button when pressed or done, and the keyboard focus outline.

### Neutral
- **Printing Ink** (printing-ink): all text, every rule and border, the primary button face, the command slip, the footer band, and the `::selection` background.
- **Ink Soft** (ink-soft): secondary prose on paper and on yellow (section intros, hints, the order form's note).
- **Muted** (muted): print captions and prompt text beneath prints.
- **Print Paper** (print-paper): page background, print borders, tick-box fill, the drop-off row and the say slip.
- **Paper Shade** (paper-shade): hover fill on a drop-off row cell.
- **Hairline** (hairline): the 1px edge of a print sheet, the only border lighter than full ink.
- **Slip Text / Slip Comment** (slip-text, slip-comment): text and comment lines on the black command slip.
- **Envelope Bright** (envelope-bright): hover on the copy button only.

### Named Rules
**The Reserved Red Rule.** Lab red means "marked by hand": ticked, pressed, focused. It never colours a heading, a background band, a link or decoration. If a red element is not in one of those three states, it is wrong.

**The Whole Band Rule.** Envelope yellow is a stock, not an accent. It owns full-width bands or solid controls; it is never a tint, a gradient, or a highlight behind a word.

## Typography

**Display Font:** Sofia Sans Extra Condensed 800 (with Sofia Sans, system sans)
**Body Font:** Sofia Sans, variable (with ui-sans-serif, system-ui)
**Label/Mono Font:** JetBrains Mono (with ui-monospace, SF Mono, Menlo)

All three are self-hosted woff2 in `docs/fonts`; the display face is preloaded.

**Character:** A tall, tightly packed uppercase grotesk that reads as the lab's printed envelope, against a friendly, plain sans that carries the explanations. The mono is the lab's machines talking.

### Hierarchy
- **Display** (800, min(15.2vw, 228px), 0.78): the product name only, on one line, set flush to the envelope's left edge.
- **Headline** (800, clamp(46px, 7vw, 92px), 0.92): section heads, uppercase, balanced wrap.
- **Title** (800, 24 to 30px, 0.92): step titles, term heads, model names in the legend. Uppercase.
- **Lede** (500, clamp(19px, 2.1vw, 24px), 1.35, max 34ch): the one sentence under the name; the say slip uses the same voice at clamp(18px, 2vw, 21px).
- **Body** (400, 17px, 1.55, max about 62ch): prose; secondary prose at 15 to 18px in Ink Soft.
- **Label** (700, 11 to 12px, 0.1em, uppercase): form-column labels (Order, Size, Agent), docket captions under step numerals, the inline "Prompt" tag. Navigation uses the same voice at 600, 13px, 0.08em.
- **Mono** (500 or 400, 11 to 15px): the typed order line (15px), the back-print on prints (11 to 11.5px), command slips (14px, 1.7), the date stamp.

### Named Rules
**The Three Voices Rule.** Condensed uppercase is printed by the envelope, sans is written for a person, mono is printed by a machine. A string's role decides its face; never set prose in the condensed face or a heading in mono.

## Layout

A single centred column (max 1180px) with a fluid gutter (clamp(16px, 4vw, 48px)); colour bands run full width outside it. Sections breathe on clamp(64px, 9vw, 112px) vertical padding. Section heads are a two-column grid, headline left and a short intro right, bottom-aligned; they stack under 900px.

The header splits into the order (left, about half) and the fanned prints (right) on a 1fr / 1.05fr grid, collapsing to one column under 900px. Ruled tables use a fixed label column (92px, 64px under 560px) beside a fluid fill column. Prints sit in flex rows whose items grow by their aspect ratio, so a row of mixed formats shares one baseline height; rows wrap under 900px and stack under 560px. Terms flow in up to three newspaper columns (280px minimum).

**The One Line Rule.** The drop-off row of agent tick boxes is one unbroken line of four equal cells at every width; under 560px each cell stacks its box above its name rather than wrapping the row.

## Elevation & Depth

Flat. There is no `box-shadow` anywhere in this world. Depth is made two ways only: sheets overlapping (the three prints fanned out of the envelope, with z-order and small rotations of -6 to +5 degrees in the fan and under 1 degree on the counter), and a surface being one shade further back (Envelope Deep for the pocket and the envelope's back, with the flap drawn as a yellow triangle over it).

Motion follows the same physics: prints slide up out of the pocket on load (0.9s, cubic-bezier(.16,1,.3,1), staggered 0.05 / 0.16 / 0.27s), and a new command slip feeds out top to bottom by clip-path (0.42s) when an agent is ticked. The primary button lifts 2px on hover. All entrance motion sits behind `prefers-reduced-motion: no-preference`.

### Named Rules
**The Overlap Only Rule.** Depth is overlap or a deeper shade of the same stock. Never a drop shadow, glow or blurred halo.

## Shapes

Square corners throughout (0px); the only rounded shape is the favicon's 4px tile. Borders are full-ink 2px rules that divide forms into cells, with 1.5px rules above term blocks and a 1px hairline around a print. Tick boxes are 18px squares with a 2px ink border; the tick is two crossed lab-red strokes. The perforation is a row of 3px circles punched into the pocket's top edge. The flap is a clipped triangle. The date stamp is the one rotated rule-box (-5 degrees), a hand-inked accent.

## Components

### Buttons
Tactile and printed: solid ink slabs with no radius.
- **Shape:** square (0px), 2px ink border.
- **Primary:** Printing Ink face, Envelope Yellow text, 700 16px Sofia Sans, 16px 22px padding, a 16px stroked SVG arrow.
- **Hover / Focus:** lifts 2px (0.15s, cubic-bezier(.2,.8,.2,1)); focus is a 3px lab-red outline offset 3px.
- **Pressed:** turns Lab Red with paper text and drops back.
- **Quiet:** the second action is a plain underlined text link at 600 15px.
- **Copy:** a small yellow uppercase tag pinned to the command slip's top right; Envelope Bright on hover; Lab Red with "Copied" when done. Under 560px it drops beneath the command.

### Order Form (signature)
- A 2px ink-ruled table: uppercase label column on the left, fill column on the right. The fill carries the typed order in mono, tick boxes in a row, or a note in Ink Soft sans.

### Drop-off Row (signature)
- Four equal cells in one ruled strip on Print Paper, each a native radio with a visible square tick box and a name. Ticking crosses the box in red and underlines the name in red (3px); hovering shades the cell Paper Shade; focus outlines the box in red. Ticking reprints the command slip beneath it.

### Prints (signature)
- **Corner Style:** square.
- **Background:** Print Paper border (10 to 12px, a deeper bottom margin in the fan), 1px hairline.
- **Shadow Strategy:** none; see Elevation & Depth.
- **Caption:** back-print in mono (frame number, model, size, cost in bold), then the prompt in muted sans behind an uppercase "Prompt" tag. Images keep their true aspect ratio.

### Slips
- **Command slip:** Printing Ink block, mono 14px/1.7 in Slip Text, comment lines in Slip Comment, horizontal scroll (wrapping under 560px).
- **Say slip:** a sentence to say to the agent, in lede-sized sans on Print Paper inside a 2px ink box.

### Steps
- A ruled table like the order form: the left cell holds a large condensed numeral (44px, 34px narrow) with an uppercase docket caption; the right holds a title, prose and a slip.

### Price List (rate sheet)
A native `<dialog>` opened from the nav ("Prices") and from the hero actions ("See the price list"). It is a print-paper rate sheet pulled out of an Envelope Deep pocket that rises at the bottom of the viewport: the pocket slides up, the sheet travels up out of it with a small overshoot and settles at -0.6deg (0 under 560px), and the table rows print in one by one from the left like a lab receipt. Closing tucks the sheet back down into the pocket and the pocket drops away; Escape, the Close button and a click outside the sheet all close it. Reduced motion shows and hides it without movement. Measured prices carry a small ticked box — the Reserved Red Rule's "ticked" state, not decoration. Prices are mono with tabular numerals; model names are Title-voice group rows; the two cheapest items close the sheet in a ruled two-cell tally. The ink scrim is flat (no blur).

### Navigation
- Top: uppercase 600 13px sans links, 0.08em tracking, no underline until hover, beside the real logo file. Footer: a black band with the name in the display face (44px) and plain links in Envelope Yellow.

## Do's and Don'ts

### Do:
- **Do** let Envelope Yellow own whole full-width bands, and alternate them with Print Paper.
- **Do** divide forms with 2px Printing Ink rules and keep every corner square (0px).
- **Do** keep Lab Red for ticked, pressed and focused states only.
- **Do** show real prints with their back-print: model, size and cost in USD.
- **Do** set envelope headings in uppercase Sofia Sans Extra Condensed 800, prose in Sofia Sans, machine output in JetBrains Mono.
- **Do** gate entrance motion behind `prefers-reduced-motion: no-preference`.
- **Do** use the real logo file (`contentcoach-logo.png`), never a re-drawn wordmark.

### Don't:
- **Don't** use `box-shadow`, glows or blurred halos; depth is overlap or a deeper shade of stock.
- **Don't** frame the page as a dark dev-tool hero around a fake terminal; commands are slips inside the order.
- **Don't** round corners or soften the 2px ink rules into grey hairlines (the 1px hairline belongs to print edges only).
- **Don't** use Lab Red for headings, links, bands or decoration.
- **Don't** wrap the drop-off row onto two lines.
- **Don't** import the web app's gold, neutrals, radii or shadows into this world.
