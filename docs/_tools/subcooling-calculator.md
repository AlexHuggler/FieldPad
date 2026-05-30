---
title: "Subcooling Calculator"
short_title: "Subcooling Calculator"
description: "Free subcooling calculator for HVAC techs. Enter the liquid line saturation temperature and the liquid line temperature to get measured subcooling in °F, with target ranges for charging TXV systems."
card_blurb: "Calculate measured subcooling from liquid saturation temperature and liquid line temperature, with TXV charging targets."
tool_category: "Refrigeration"
tool_order: 2
keywords: ["subcooling calculator", "hvac subcooling", "subcool calculator", "how to calculate subcooling", "subcooling formula", "charge to subcooling"]
formula_summary: "Subcooling = Liquid Saturation Temperature (at head pressure) − Liquid Line Temperature."
how_to_name: "How to calculate subcooling"
how_to_steps:
  - { name: "Find the liquid saturation temperature", text: "Read the high-side (liquid/head) pressure and convert it to saturation temperature with the refrigerant's P/T chart." }
  - { name: "Measure the liquid line temperature", text: "Clamp an accurate thermometer on the liquid line near the condenser outlet and let it stabilize." }
  - { name: "Subtract", text: "Subcooling = liquid saturation temperature − liquid line temperature." }
  - { name: "Compare to target", text: "Charge a TXV system to the manufacturer's target subcooling, commonly around 8–12°F. Always confirm against the nameplate or data tag." }
faqs:
  - { q: "What is a good subcooling reading?", a: "Most residential TXV systems target about 8–12°F of subcooling, but you should always use the value printed on the equipment data tag or in the manufacturer's charging chart. Some systems specify as low as 6°F or as high as 15°F." }
  - { q: "How do you calculate subcooling?", a: "Subcooling = liquid saturation temperature − liquid line temperature. Read the high-side pressure, convert it to saturation temperature with the refrigerant's P/T chart, measure the liquid line temperature, then subtract." }
  - { q: "What does low subcooling mean?", a: "Low subcooling (near 0°F) usually means the system is undercharged — there isn't enough liquid stacking up in the condenser. It can also indicate a TXV overfeeding or restricted condenser airflow." }
  - { q: "What does high subcooling mean?", a: "High subcooling means too much liquid is backing up in the condenser, most commonly from an overcharge. A restriction in the liquid line or a partially blocked filter-drier can also raise subcooling." }
---

<div class="not-prose bg-white border border-gray-200 rounded-2xl p-6 my-8 shadow-sm">
  <form id="sc-form" class="grid grid-cols-1 sm:grid-cols-2 gap-5">
    <div>
      <label class="calc-label" for="sc-sat">Liquid saturation temperature (°F)</label>
      <input class="calc-field" type="number" inputmode="decimal" step="any" id="sc-sat" placeholder="e.g. 105">
    </div>
    <div>
      <label class="calc-label" for="sc-line">Liquid line temperature (°F)</label>
      <input class="calc-field" type="number" inputmode="decimal" step="any" id="sc-line" placeholder="e.g. 95">
    </div>
    <div class="sm:col-span-2">
      <label class="calc-label" for="sc-target">Manufacturer target subcooling (°F, optional)</label>
      <input class="calc-field" type="number" inputmode="decimal" step="any" id="sc-target" placeholder="e.g. 10">
    </div>
    <div class="sm:col-span-2 flex flex-wrap gap-3 items-center">
      <button class="calc-btn" type="submit">Calculate subcooling</button>
      <button class="text-sm text-gray-500 hover:text-fieldpad-700" type="reset">Reset</button>
    </div>
  </form>
  <div id="sc-result" class="calc-result mt-5" aria-live="polite"></div>
</div>

<script>
(function () {
  var form = document.getElementById('sc-form');
  var out  = document.getElementById('sc-result');
  if (!form) return;
  form.addEventListener('submit', function (e) {
    e.preventDefault();
    var sat    = parseFloat(document.getElementById('sc-sat').value);
    var line   = parseFloat(document.getElementById('sc-line').value);
    var target = parseFloat(document.getElementById('sc-target').value);
    if (isNaN(sat) || isNaN(line)) {
      out.innerHTML = '<span class="calc-status calc-status-warn">Enter both the liquid saturation temperature and the liquid line temperature.</span>';
      return;
    }
    var sc = sat - line;
    var html = 'Measured subcooling: <span class="calc-result-value">' + sc.toFixed(1) + ' &deg;F</span>';
    if (!isNaN(target)) {
      var diff = sc - target;
      if (Math.abs(diff) <= 3) {
        html += '<span class="calc-status calc-status-ok">Within about 3&deg;F of the ' + target.toFixed(0) + '&deg;F target — charge looks correct.</span>';
      } else if (diff < 0) {
        html += '<span class="calc-status calc-status-alert">About ' + Math.abs(diff).toFixed(1) + '&deg;F below the ' + target.toFixed(0) + '&deg;F target — likely undercharged. Add refrigerant slowly and recheck.</span>';
      } else {
        html += '<span class="calc-status calc-status-alert">About ' + diff.toFixed(1) + '&deg;F above the ' + target.toFixed(0) + '&deg;F target — likely overcharged. Recover refrigerant slowly and recheck.</span>';
      }
    } else {
      if (sc < 5) {
        html += '<span class="calc-status calc-status-warn">On the low side. Many TXV systems target ~8–12&deg;F — confirm against the equipment data tag; low subcooling often means undercharge.</span>';
      } else if (sc <= 15) {
        html += '<span class="calc-status calc-status-ok">In the common 8–12&deg;F range for many TXV systems. Always confirm against the equipment data tag.</span>';
      } else {
        html += '<span class="calc-status calc-status-warn">On the high side. High subcooling often means overcharge or a liquid-line restriction — confirm against the equipment data tag.</span>';
      }
    }
    out.innerHTML = html;
  });
  form.addEventListener('reset', function () { out.innerHTML = ''; });
})();
</script>

## How the subcooling calculation works

**Subcooling** is the number of degrees the liquid refrigerant has been cooled *below* its saturation (condensing) temperature at the high-side pressure. It tells you how much liquid is "stacking up" in the condenser — which is a direct indicator of system charge on a TXV system.

The formula is:

**Subcooling (°F) = Liquid Saturation Temperature − Liquid Line Temperature**

The liquid saturation temperature comes from converting the high-side (head) pressure with the refrigerant's P/T chart. The liquid line temperature is measured with a clamp thermometer at the condenser outlet.

### Worked example

A R-410A system shows a head pressure that converts to a **105°F** liquid saturation temperature, and the liquid line measures **95°F**:

**105 − 95 = 10°F of subcooling**

If the data tag calls for 10°F, the charge is right on target.

## Charging to subcooling

On a **TXV or EEV system**, subcooling is the primary charging target because the metering valve already controls superheat. Add refrigerant to raise subcooling, recover refrigerant to lower it, and always charge to the **value printed on the equipment data tag** rather than a generic number.

| Reading | Likely meaning |
|---|---|
| Low subcooling (under ~5°F) | Undercharge, or condenser airflow / TXV overfeed |
| On target (per data tag, often 8–12°F) | Correct charge |
| High subcooling (over ~15°F) | Overcharge, or a liquid-line restriction |

> Read subcooling together with superheat. On a fixed-orifice system you charge to **superheat** instead — use the [Superheat Calculator]({{ "/tools/superheat-calculator/" | relative_url }}) and the [Target Superheat Calculator]({{ "/tools/target-superheat-calculator/" | relative_url }}).

## How FieldPad helps in the field

This calculator gives you the number instantly. **FieldPad** keeps the charging record with the equipment: log the subcooling reading against the system and the client, attach it to the job, and roll it straight into a signed invoice. Next season, the system's charging history is one tap away on the same record.
