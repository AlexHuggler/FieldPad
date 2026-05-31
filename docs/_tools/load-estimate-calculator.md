---
title: "HVAC Load Estimate Calculator (Square Footage)"
short_title: "Load Estimate Calculator"
description: "Free rule-of-thumb HVAC sizing calculator. Estimate ballpark cooling BTU and tonnage from square footage and climate — for quick budgeting only, not a substitute for a Manual J load calculation."
card_blurb: "Rough ballpark cooling BTU and tonnage from square footage and climate — for budgeting only, never final sizing."
tool_category: "Load & Sizing"
tool_order: 1
keywords: ["hvac load calculator", "btu per square foot", "ac sizing calculator", "how many tons ac", "square footage cooling estimate", "tonnage calculator"]
formula_summary: "Estimated BTU/h ≈ area (sq ft) × climate factor (BTU/sq ft) × insulation modifier. Tons = BTU/h ÷ 12,000. Rule of thumb only — not a Manual J."
how_to_name: "How to estimate HVAC size from square footage"
how_to_steps:
  - { name: "Enter conditioned area", text: "Enter the conditioned floor area in square feet (the space the system actually serves)." }
  - { name: "Pick a climate band", text: "Choose the climate that fits the location — cooler climates need fewer BTU per square foot, hotter climates more." }
  - { name: "Set the insulation level", text: "Adjust for building quality: tight new construction needs less, older/leaky homes need more." }
  - { name: "Read the ballpark", text: "Estimated BTU/h ÷ 12,000 = tons. Use this only for budgeting, then commission a Manual J load calculation." }
faqs:
  - { q: "How many BTU do I need per square foot?", a: "A common rule of thumb is about 20–25 BTU per square foot for an average home in a moderate climate, less in cool climates (18–22) and more in hot climates (28–35 or higher). This is only a ballpark — real sizing depends on insulation, windows, orientation, infiltration, and local design temperatures." }
  - { q: "How many tons of AC for 2,000 square feet?", a: "Using the rule of thumb, 2,000 sq ft × 20–25 BTU/sq ft is roughly 40,000–50,000 BTU/h, or about 3.5–4 tons in a moderate climate. But this is a rough estimate only — a Manual J load calculation often yields a meaningfully different (frequently smaller) number." }
  - { q: "Is this calculator accurate enough to size equipment?", a: "No. Rule-of-thumb square-footage sizing can be off by 30–50% because it ignores insulation R-values, window quality and orientation, air infiltration, duct losses, and local design conditions. Use it only for early budgeting; always get a Manual J residential load calculation (or a commercial load analysis) before purchasing equipment." }
  - { q: "Why is oversizing AC a problem?", a: "An oversized system short-cycles: it cools the air quickly but doesn't run long enough to remove humidity, leaving the space cold and clammy. It also wears out faster and costs more upfront. A right-sized system from a proper load calculation runs longer, dehumidifies better, and lasts longer." }
---

<div class="not-prose bg-white border border-gray-200 rounded-2xl p-6 my-8 shadow-sm">
  <div class="calc-status calc-status-alert" style="margin-top:0;margin-bottom:1.25rem;">
    <strong>This is a rough estimate, not a system design.</strong> Rule-of-thumb sizing can be off by 30–50%. It does <strong>not</strong> replace a Manual J load calculation and must never be used to actually select equipment. Use it for ballpark budgeting only, then have a professional run a Manual J (residential) or load analysis (commercial) before buying anything.
  </div>
  <form id="le-form" class="grid grid-cols-1 sm:grid-cols-3 gap-5">
    <div>
      <label class="calc-label" for="le-area">Conditioned area (sq ft)</label>
      <input class="calc-field" type="number" inputmode="decimal" step="any" id="le-area" placeholder="e.g. 2000">
    </div>
    <div>
      <label class="calc-label" for="le-climate">Climate</label>
      <select class="calc-field" id="le-climate">
        <option value="20">Cool (18–22 BTU/sq ft)</option>
        <option value="22" selected>Moderate (20–25)</option>
        <option value="31">Hot (28–35)</option>
        <option value="45">Very hot / desert (40–50)</option>
      </select>
    </div>
    <div>
      <label class="calc-label" for="le-ins">Insulation / construction</label>
      <select class="calc-field" id="le-ins">
        <option value="0.85">Tight / new (×0.85)</option>
        <option value="1.0" selected>Average (×1.0)</option>
        <option value="1.3">Older / leaky (×1.3)</option>
      </select>
    </div>
    <div class="sm:col-span-3 flex flex-wrap gap-3 items-center">
      <button class="calc-btn" type="submit">Estimate size</button>
      <button class="text-sm text-gray-500 hover:text-fieldpad-700" type="reset">Reset</button>
    </div>
  </form>
  <div id="le-result" class="calc-result mt-5" aria-live="polite"></div>
</div>

<script>
(function () {
  var form = document.getElementById('le-form');
  var out  = document.getElementById('le-result');
  if (!form) return;
  form.addEventListener('submit', function (e) {
    e.preventDefault();
    var area = parseFloat(document.getElementById('le-area').value);
    var f    = parseFloat(document.getElementById('le-climate').value);
    var ins  = parseFloat(document.getElementById('le-ins').value);
    if (isNaN(area) || area <= 0) {
      out.innerHTML = '<span class="calc-status calc-status-warn">Enter the conditioned area in square feet.</span>';
      return;
    }
    var btu = area * f * ins;
    var tons = btu / 12000;
    // Show a +/- range to reinforce that this is a ballpark
    var lowTons = (btu * 0.85) / 12000;
    var highTons = (btu * 1.15) / 12000;
    out.innerHTML =
      'Ballpark cooling load: <span class="calc-result-value">' + Math.round(btu / 500) * 500 + ' BTU/h</span>' +
      '<div class="mt-1 text-gray-600">&asymp; ' + tons.toFixed(1) + ' tons (rough range ' + lowTons.toFixed(1) + '–' + highTons.toFixed(1) + ' tons)</div>' +
      '<span class="calc-status calc-status-alert">Estimate only — do not buy equipment from this number. Get a Manual J load calculation first.</span>';
  });
  form.addEventListener('reset', function () { out.innerHTML = ''; });
})();
</script>

## How the square-footage estimate works

A long-standing field rule of thumb sizes cooling from conditioned area and a per-square-foot BTU factor:

**Estimated BTU/h ≈ Area (sq ft) × Climate factor (BTU/sq ft) × Insulation modifier**

**Tons = BTU/h ÷ 12,000**

| Climate | BTU/sq ft |
|---|---|
| Cool | 18–22 |
| Moderate | 20–25 |
| Hot | 28–35 |
| Very hot / desert | 40–50+ |

Insulation modifier: tight/new construction ≈ ×0.85, average ≈ ×1.0, older/leaky ≈ ×1.3.

### Worked example

**2,000 sq ft**, moderate climate (≈22 BTU/sq ft), average insulation:

**2,000 × 22 × 1.0 = 44,000 BTU/h ≈ 3.7 tons** — a *ballpark* only.

## Why this is only a starting point

Rule-of-thumb sizing **ignores** the things that actually drive a load: insulation R-values, window U-factors and orientation, air infiltration, duct losses, internal gains, and local design temperatures. As a result it can be off by **30–50%**, and it tends to **oversize** — which causes short cycling, poor dehumidification, and premature wear.

> **Always commission a Manual J** residential load calculation (or a commercial load analysis) before selecting equipment. Most manufacturers' warranties and many building codes now require a certified load calculation. Use this tool only for early budgeting and feasibility.

## How FieldPad helps in the field

Give a customer a ballpark here, then move the real work into **FieldPad** — log the equipment, attach the proper load calculation, and turn an accurate, right-sized recommendation into a signed estimate. One offline-ready app, full history on every client and system.
