---
title: "Line-Set Charge Calculator"
short_title: "Line-Set Charge Calculator"
description: "Free refrigerant line-set charge calculator. Estimate the extra refrigerant to add per foot of liquid line beyond the factory-charged length, by liquid line diameter."
card_blurb: "Estimate the additional refrigerant charge for liquid line beyond the factory-charged length, by line diameter."
tool_category: "Refrigeration"
tool_order: 5
keywords: ["line set charge calculator", "refrigerant line charge", "liquid line charge per foot", "additional refrigerant charge", "line set length charge"]
formula_summary: "Additional charge (oz) = oz-per-foot (by liquid line OD) × (total line length − factory-charged length). 1/4\" ≈ 0.27, 5/16\" ≈ 0.40, 3/8\" ≈ 0.60 oz/ft."
how_to_name: "How to calculate line-set charge adjustment"
how_to_steps:
  - { name: "Find the factory-charged length", text: "Check the unit's data tag or install manual for the line length the factory charge covers (commonly 15–25 ft)." }
  - { name: "Measure the actual liquid line length", text: "Measure the total installed liquid line run from the condenser to the evaporator." }
  - { name: "Select the liquid line diameter", text: "Identify the liquid line outside diameter (1/4\", 5/16\", or 3/8\")." }
  - { name: "Multiply the extra feet by the per-foot rate", text: "Additional charge = oz-per-foot × (total length − factory-charged length). Always verify the final charge by subcooling or superheat." }
faqs:
  - { q: "How much refrigerant do you add per foot of line set?", a: "It depends on the liquid line diameter. Common figures are about 0.27 oz/ft for 1/4\" line, 0.40 oz/ft for 5/16\", and 0.60 oz/ft for 3/8\". You only add for liquid line length beyond what the factory charge already covers, and the manufacturer's published value always governs." }
  - { q: "Do you add charge for the suction (vapor) line?", a: "Generally no. The liquid line holds the vast majority of the additional refrigerant mass; the vapor line contains comparatively little. Most residential charge-adjustment tables are based on liquid line length only — but check the manufacturer's instructions, as some equipment specifies otherwise." }
  - { q: "How long is a factory line-set charge good for?", a: "Most split systems ship pre-charged for a baseline line length, commonly 15 to 25 feet. Beyond that baseline you add refrigerant per foot; below it some manufacturers have you remove a small amount. Always use the value on the data tag or in the install manual." }
  - { q: "Is the calculated line charge exact?", a: "No — treat it as a starting estimate. The per-foot figures are industry standards, but refrigerant type, ambient conditions, and the specific equipment all matter. Always confirm the final charge with the subcooling method (TXV systems) or superheat (fixed-orifice systems)." }
---

<div class="not-prose bg-white border border-gray-200 rounded-2xl p-6 my-8 shadow-sm">
  <form id="ls-form" class="grid grid-cols-1 sm:grid-cols-2 gap-5">
    <div>
      <label class="calc-label" for="ls-dia">Liquid line diameter (OD)</label>
      <select class="calc-field" id="ls-dia">
        <option value="0.27">1/4"</option>
        <option value="0.40">5/16"</option>
        <option value="0.60">3/8"</option>
      </select>
    </div>
    <div>
      <label class="calc-label" for="ls-total">Total liquid line length (ft)</label>
      <input class="calc-field" type="number" inputmode="decimal" step="any" id="ls-total" placeholder="e.g. 45">
    </div>
    <div class="sm:col-span-2">
      <label class="calc-label" for="ls-base">Factory-charged length (ft)</label>
      <input class="calc-field" type="number" inputmode="decimal" step="any" id="ls-base" value="15">
      <p class="text-xs text-gray-400 mt-1">From the data tag / install manual — commonly 15–25 ft.</p>
    </div>
    <div class="sm:col-span-2 flex flex-wrap gap-3 items-center">
      <button class="calc-btn" type="submit">Calculate charge</button>
      <button class="text-sm text-gray-500 hover:text-fieldpad-700" type="reset">Reset</button>
    </div>
  </form>
  <div id="ls-result" class="calc-result mt-5" aria-live="polite"></div>
</div>

<script>
(function () {
  var form = document.getElementById('ls-form');
  var out  = document.getElementById('ls-result');
  if (!form) return;
  form.addEventListener('submit', function (e) {
    e.preventDefault();
    var rate  = parseFloat(document.getElementById('ls-dia').value);
    var total = parseFloat(document.getElementById('ls-total').value);
    var base  = parseFloat(document.getElementById('ls-base').value);
    if (isNaN(base)) base = 0;
    if (isNaN(total)) {
      out.innerHTML = '<span class="calc-status calc-status-warn">Enter the total liquid line length.</span>';
      return;
    }
    var extra = total - base;
    if (extra <= 0) {
      out.innerHTML = '<span class="calc-status calc-status-ok">Line length is within the factory-charged baseline — no additional charge from line length. If the run is shorter than baseline, check the manufacturer\'s instructions for any charge to remove.</span>';
      return;
    }
    var oz = rate * extra;
    var lb = Math.floor(oz / 16);
    var rem = oz - lb * 16;
    var lboz = lb > 0 ? (lb + ' lb ' + rem.toFixed(1) + ' oz') : (oz.toFixed(1) + ' oz');
    out.innerHTML = 'Additional charge for ' + extra.toFixed(0) + ' ft of liquid line: <span class="calc-result-value">' + oz.toFixed(1) + ' oz</span>' +
      '<div class="mt-1 text-gray-600">(' + lboz + ')</div>' +
      '<span class="calc-status calc-status-warn">Estimate only. The manufacturer\'s charge table governs — always verify the final charge by subcooling or superheat.</span>';
  });
  form.addEventListener('reset', function () { out.innerHTML = ''; });
})();
</script>

## How the line-set charge calculation works

A split system ships pre-charged for a **baseline liquid line length** (commonly 15–25 ft). When the installed run is longer, you add refrigerant for the extra liquid line — and almost all of that added mass lives in the **liquid line**, so the adjustment is based on liquid line diameter:

**Additional charge (oz) = oz-per-foot × (total liquid line length − factory-charged length)**

| Liquid line OD | Typical charge rate |
|---|---|
| 1/4" | ~0.27 oz/ft |
| 5/16" | ~0.40 oz/ft |
| 3/8" | ~0.60 oz/ft |

### Worked example

A system with a **3/8"** liquid line has a **45 ft** run and a factory baseline of **15 ft**:

**0.60 oz/ft × (45 − 15) = 0.60 × 30 = 18 oz** (1 lb 2 oz) of refrigerant to add.

## Important cautions

- **The manufacturer's charge table governs.** Different equipment and refrigerants (R-410A, R-454B, R-22) can specify different per-foot values — always use the data tag or install manual when available.
- **This is a starting estimate, not the final charge.** Confirm the actual charge by [subcooling]({{ "/tools/subcooling-calculator/" | relative_url }}) on a TXV system or by [target superheat]({{ "/tools/target-superheat-calculator/" | relative_url }}) on a fixed-orifice system.
- The adjustment is for **liquid line** length; most residential tables omit the vapor line, but check the manufacturer's instructions for your unit.

## How FieldPad helps in the field

Estimate the line-set charge here, then record the actual charge in **FieldPad** against the equipment and the install job. Log the line length, the refrigerant, and the final verified charge on one record, and roll it straight into the install invoice — offline, on your iPhone.
