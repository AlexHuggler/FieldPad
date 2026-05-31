---
title: "Compression Ratio Calculator"
short_title: "Compression Ratio Calculator"
description: "Free compression ratio calculator for HVAC/R techs. Enter discharge and suction pressures to get the absolute compression ratio, with healthy-range guidance and what a high ratio means."
card_blurb: "Calculate a system's compression ratio from discharge and suction pressure, with healthy-range guidance."
tool_category: "Refrigeration"
tool_order: 4
keywords: ["compression ratio calculator", "hvac compression ratio", "refrigeration compression ratio", "discharge suction pressure ratio", "compressor ratio formula"]
formula_summary: "Compression Ratio = (Discharge PSIG + 14.7) ÷ (Suction PSIG + 14.7) — the ratio of absolute pressures."
how_to_name: "How to calculate compression ratio"
how_to_steps:
  - { name: "Read the discharge pressure", text: "Read the high-side (discharge) gauge pressure in PSIG with the system running at steady state." }
  - { name: "Read the suction pressure", text: "Read the low-side (suction) gauge pressure in PSIG." }
  - { name: "Convert to absolute", text: "Add 14.7 (atmospheric pressure at sea level) to each gauge reading to get absolute pressure (PSIA)." }
  - { name: "Divide", text: "Compression ratio = discharge PSIA ÷ suction PSIA. The result is a unitless ratio, e.g. 3.2:1." }
faqs:
  - { q: "What is a good compression ratio for HVAC?", a: "Most air conditioning and refrigeration systems run a compression ratio of roughly 2.3:1 to 3.5:1 under normal cooling conditions. A ratio above about 4:1 signals trouble — the compressor is working harder, drawing more amps, and running hotter, which shortens its life." }
  - { q: "How do you calculate compression ratio?", a: "Compression ratio = (discharge pressure in PSIG + 14.7) ÷ (suction pressure in PSIG + 14.7). You must convert the gauge readings to absolute pressure by adding atmospheric pressure (14.7 PSI at sea level) before dividing." }
  - { q: "Why do you add 14.7 to the pressures?", a: "Gauge pressure (PSIG) reads zero at atmospheric pressure, but compression is a ratio of true absolute pressures. Adding 14.7 PSI (sea-level atmospheric pressure) converts gauge readings to absolute pressure (PSIA) so the ratio is physically correct." }
  - { q: "What does a high compression ratio mean?", a: "A high ratio comes from low suction pressure (undercharge, restriction, low load, or poor evaporator heat transfer), high discharge pressure (dirty condenser, failed condenser fan, overcharge, or non-condensables), or both. It reduces capacity and efficiency and stresses the compressor. The ratio flags a problem but doesn't identify the cause — diagnose pressures individually." }
---

<div class="not-prose bg-white border border-gray-200 rounded-2xl p-6 my-8 shadow-sm">
  <form id="cr-form" class="grid grid-cols-1 sm:grid-cols-2 gap-5">
    <div>
      <label class="calc-label" for="cr-disc">Discharge pressure (PSIG)</label>
      <input class="calc-field" type="number" inputmode="decimal" step="any" id="cr-disc" placeholder="e.g. 260">
    </div>
    <div>
      <label class="calc-label" for="cr-suct">Suction pressure (PSIG)</label>
      <input class="calc-field" type="number" inputmode="decimal" step="any" id="cr-suct" placeholder="e.g. 70">
    </div>
    <div class="sm:col-span-2">
      <label class="calc-label" for="cr-atm">Atmospheric pressure (PSI)</label>
      <input class="calc-field" type="number" inputmode="decimal" step="any" id="cr-atm" value="14.7">
      <p class="text-xs text-gray-400 mt-1">Default 14.7 at sea level. Lower it at high altitude for more accuracy.</p>
    </div>
    <div class="sm:col-span-2 flex flex-wrap gap-3 items-center">
      <button class="calc-btn" type="submit">Calculate ratio</button>
      <button class="text-sm text-gray-500 hover:text-fieldpad-700" type="reset">Reset</button>
    </div>
  </form>
  <div id="cr-result" class="calc-result mt-5" aria-live="polite"></div>
</div>

<script>
(function () {
  var form = document.getElementById('cr-form');
  var out  = document.getElementById('cr-result');
  if (!form) return;
  form.addEventListener('submit', function (e) {
    e.preventDefault();
    var disc = parseFloat(document.getElementById('cr-disc').value);
    var suct = parseFloat(document.getElementById('cr-suct').value);
    var atm  = parseFloat(document.getElementById('cr-atm').value);
    if (isNaN(atm)) atm = 14.7;
    if (isNaN(disc) || isNaN(suct)) {
      out.innerHTML = '<span class="calc-status calc-status-warn">Enter both the discharge and suction pressures.</span>';
      return;
    }
    var suctAbs = suct + atm;
    if (suctAbs <= 0) {
      out.innerHTML = '<span class="calc-status calc-status-warn">Suction absolute pressure must be greater than zero. Check the suction reading (deep vacuum?).</span>';
      return;
    }
    var ratio = (disc + atm) / suctAbs;
    var html = 'Compression ratio: <span class="calc-result-value">' + ratio.toFixed(2) + ' : 1</span>';
    if (ratio < 2) {
      html += '<span class="calc-status calc-status-warn">Lower than typical. Verify readings — could be light load or a flooded condition.</span>';
    } else if (ratio <= 3.5) {
      html += '<span class="calc-status calc-status-ok">In the typical healthy range (about 2.3:1 to 3.5:1) for normal cooling conditions.</span>';
    } else if (ratio <= 4) {
      html += '<span class="calc-status calc-status-warn">On the high side. Check the condenser, charge, and suction conditions before they push it past 4:1.</span>';
    } else {
      html += '<span class="calc-status calc-status-alert">Above 4:1 — the compressor is under mechanical and thermal stress (higher amp draw, more heat). Diagnose high head and/or low suction before continuing.</span>';
    }
    out.innerHTML = html;
  });
  form.addEventListener('reset', function () { out.innerHTML = ''; });
})();
</script>

## How the compression ratio calculation works

**Compression ratio** is how many times the compressor multiplies the absolute pressure of the refrigerant from the suction side to the discharge side. It's a quick gauge of how hard the compressor is working:

**Compression Ratio = (Discharge PSIG + 14.7) ÷ (Suction PSIG + 14.7)**

You **must** convert gauge pressure to absolute pressure first. Gauges read `0` at atmospheric pressure, but the compressor sees true absolute pressure, so you add **14.7 PSI** (sea-level atmospheric) to each reading before dividing. At higher altitude, atmospheric pressure is lower — drop the constant accordingly for a more precise number.

### Worked example

A system reads **260 PSIG** discharge and **70 PSIG** suction:

**(260 + 14.7) ÷ (70 + 14.7) = 274.7 ÷ 84.7 ≈ 3.24 : 1**

That's a healthy ratio.

## Reading the result

| Ratio | Meaning |
|---|---|
| ~2.3 : 1 – 3.5 : 1 | Typical, healthy operating range |
| 3.5 : 1 – 4 : 1 | Elevated — investigate condenser, charge, suction |
| Over 4 : 1 | Mechanical/thermal stress; amp draw climbs 15–25%; risk of valve and bearing wear |

A high ratio is driven by **low suction** (undercharge, restriction, low load, poor evaporator heat transfer), **high discharge** (dirty condenser, dead condenser fan, overcharge, non-condensables), or both. The ratio tells you *something* is wrong — read the high-side and low-side pressures individually to find *what*.

> Never add refrigerant just because the ratio is high. Confirm the root cause first — pair this with [superheat]({{ "/tools/superheat-calculator/" | relative_url }}) and [subcooling]({{ "/tools/subcooling-calculator/" | relative_url }}), and use the [PT chart]({{ "/tools/pt-chart-calculator/" | relative_url }}) to convert pressures to saturation temperatures.

## How FieldPad helps in the field

Run the ratio here, then keep the diagnosis where the job lives. **FieldPad** logs your pressure readings and findings against the equipment and the client, attaches them to the service job, and turns the repair into a signed estimate or invoice — offline, on your iPhone, with the full history on one record.
