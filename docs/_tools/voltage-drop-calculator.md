---
title: "Voltage Drop Calculator"
short_title: "Voltage Drop Calculator"
description: "Free voltage drop calculator for single- and three-phase circuits. Enter conductor material, wire gauge, length, and current to get voltage drop and percent drop against NEC guidance."
card_blurb: "Calculate single- or three-phase voltage drop and percent drop from wire gauge, length, and current."
tool_category: "Electrical"
tool_order: 3
keywords: ["voltage drop calculator", "nec voltage drop", "wire size voltage drop", "3 percent voltage drop", "conductor voltage drop"]
formula_summary: "Single-phase VD = (2 × K × L × I) ÷ CM; three-phase VD = (1.732 × K × L × I) ÷ CM. K = 12.9 (Cu) or 21.2 (Al); CM = circular mils. %VD = VD ÷ source voltage × 100."
how_to_name: "How to calculate voltage drop"
how_to_steps:
  - { name: "Choose material and gauge", text: "Select copper or aluminum and the conductor size; the calculator looks up its circular mils." }
  - { name: "Enter length and current", text: "Enter the one-way circuit length in feet and the load current in amps." }
  - { name: "Pick the phase", text: "Choose single-phase or three-phase; the formula uses a factor of 2 for single-phase and 1.732 for three-phase." }
  - { name: "Read the result", text: "The tool gives voltage drop in volts and percent of the source voltage, flagged against the NEC's 3%/5% recommendations." }
faqs:
  - { q: "How do you calculate voltage drop?", a: "For single-phase: VD = (2 × K × L × I) ÷ CM. For three-phase: VD = (1.732 × K × L × I) ÷ CM. K is 12.9 for copper or 21.2 for aluminum, L is the one-way length in feet, I is the current in amps, and CM is the conductor's circular mils. Percent drop = VD ÷ source voltage × 100." }
  - { q: "What is an acceptable voltage drop?", a: "The NEC includes informational notes recommending no more than 3% voltage drop on a branch circuit and no more than 5% on the combined feeder and branch. These are recommendations, not enforceable requirements, though many inspectors and equipment manufacturers treat them as limits." }
  - { q: "Is the 3% voltage drop limit required by code?", a: "No — the 3% and 5% figures appear as informational notes in the NEC, not as mandatory rules. However, some jurisdictions adopt them, and equipment warranties may require them, so always verify with the authority having jurisdiction." }
  - { q: "What are K and circular mils?", a: "K is the resistivity constant for the conductor material (about 12.9 for copper, 21.2 for aluminum at 75°C). Circular mils (CM) is the conductor's cross-sectional area — for example, 12 AWG is 6,530 CM and 10 AWG is 10,380 CM. Larger conductors have more circular mils and less voltage drop." }
---

<div class="not-prose bg-white border border-gray-200 rounded-2xl p-6 my-8 shadow-sm">
  <form id="vd-form" class="grid grid-cols-1 sm:grid-cols-2 gap-5">
    <div>
      <label class="calc-label" for="vd-mat">Conductor material</label>
      <select class="calc-field" id="vd-mat">
        <option value="12.9">Copper (K = 12.9)</option>
        <option value="21.2">Aluminum (K = 21.2)</option>
      </select>
    </div>
    <div>
      <label class="calc-label" for="vd-awg">Wire size</label>
      <select class="calc-field" id="vd-awg">
        <option value="4107">14 AWG</option>
        <option value="6530" selected>12 AWG</option>
        <option value="10380">10 AWG</option>
        <option value="16510">8 AWG</option>
        <option value="26240">6 AWG</option>
        <option value="41740">4 AWG</option>
        <option value="66360">2 AWG</option>
        <option value="83690">1 AWG</option>
        <option value="105600">1/0 AWG</option>
        <option value="133100">2/0 AWG</option>
        <option value="167800">3/0 AWG</option>
        <option value="211600">4/0 AWG</option>
      </select>
    </div>
    <div>
      <label class="calc-label" for="vd-len">One-way length (ft)</label>
      <input class="calc-field" type="number" inputmode="decimal" step="any" id="vd-len" placeholder="e.g. 150">
    </div>
    <div>
      <label class="calc-label" for="vd-amp">Load current (A)</label>
      <input class="calc-field" type="number" inputmode="decimal" step="any" id="vd-amp" placeholder="e.g. 30">
    </div>
    <div>
      <label class="calc-label" for="vd-phase">Phase</label>
      <select class="calc-field" id="vd-phase">
        <option value="2">Single-phase</option>
        <option value="1.732">Three-phase</option>
      </select>
    </div>
    <div>
      <label class="calc-label" for="vd-src">Source voltage (V)</label>
      <input class="calc-field" type="number" inputmode="decimal" step="any" id="vd-src" placeholder="e.g. 240">
    </div>
    <div class="sm:col-span-2 flex flex-wrap gap-3 items-center">
      <button class="calc-btn" type="submit">Calculate drop</button>
      <button class="text-sm text-gray-500 hover:text-fieldpad-700" type="reset">Reset</button>
    </div>
  </form>
  <div id="vd-result" class="calc-result mt-5" aria-live="polite"></div>
</div>

<script>
(function () {
  var form = document.getElementById('vd-form');
  var out  = document.getElementById('vd-result');
  if (!form) return;
  form.addEventListener('submit', function (e) {
    e.preventDefault();
    var K   = parseFloat(document.getElementById('vd-mat').value);
    var cm  = parseFloat(document.getElementById('vd-awg').value);
    var L   = parseFloat(document.getElementById('vd-len').value);
    var I   = parseFloat(document.getElementById('vd-amp').value);
    var ph  = parseFloat(document.getElementById('vd-phase').value);
    var src = parseFloat(document.getElementById('vd-src').value);
    if (isNaN(L) || isNaN(I)) {
      out.innerHTML = '<span class="calc-status calc-status-warn">Enter the circuit length and the load current.</span>';
      return;
    }
    var vd = (ph * K * L * I) / cm;
    var html = 'Voltage drop: <span class="calc-result-value">' + vd.toFixed(2) + ' V</span>';
    if (!isNaN(src) && src > 0) {
      var pct = (vd / src) * 100;
      html += '<div class="mt-1 text-gray-600">' + pct.toFixed(2) + '% of ' + src + ' V (' + (src - vd).toFixed(1) + ' V at the load)</div>';
      if (pct <= 3) html += '<span class="calc-status calc-status-ok">Within the NEC-recommended 3% for branch circuits.</span>';
      else if (pct <= 5) html += '<span class="calc-status calc-status-warn">Over 3% (branch recommendation) but within the 5% total recommendation. Consider upsizing the conductor.</span>';
      else html += '<span class="calc-status calc-status-alert">Over the NEC-recommended 5% total — upsize the conductor or shorten the run. Note: these are NEC informational recommendations, not mandates; verify with your AHJ.</span>';
    } else {
      html += '<span class="calc-status calc-status-ok">Enter the source voltage to see percent drop against the NEC 3%/5% recommendations.</span>';
    }
    out.innerHTML = html;
  });
  form.addEventListener('reset', function () { out.innerHTML = ''; });
})();
</script>

## How the voltage drop calculation works

Voltage drop is the voltage lost to conductor resistance over the length of a run. The standard field formula uses the conductor's circular mils and a material constant:

**Single-phase: VD = (2 × K × L × I) ÷ CM**

**Three-phase: VD = (1.732 × K × L × I) ÷ CM**

- **K** = resistivity constant: **12.9** for copper, **21.2** for aluminum (at ~75°C)
- **L** = one-way circuit length in feet
- **I** = load current in amps
- **CM** = conductor circular mils (looked up from the wire size)

Then **% drop = VD ÷ source voltage × 100**.

### Worked example

**30 A** down **150 ft** of **10 AWG copper** (CM = 10,380), single-phase:

**VD = (2 × 12.9 × 150 × 30) ÷ 10,380 = 116,100 ÷ 10,380 ≈ 11.2 V**

On a 240 V circuit that's about **4.7%** — over the 3% branch recommendation, so you'd consider upsizing to 8 AWG.

### Circular mils reference

| AWG | Circular mils |
|---|---|
| 14 | 4,107 |
| 12 | 6,530 |
| 10 | 10,380 |
| 8 | 16,510 |
| 6 | 26,240 |
| 4 | 41,740 |
| 2 | 66,360 |
| 1/0 | 105,600 |
| 2/0 | 133,100 |
| 4/0 | 211,600 |

## A note on the NEC

The **3% (branch) and 5% (total) figures are informational notes in the NEC — recommendations, not enforceable requirements.** Many inspectors and equipment manufacturers still treat them as limits, so always verify with the **authority having jurisdiction (AHJ)**. This calculator assumes a steady-state, ~75°C conductor; consult a licensed electrician for actual installations.

> Working the rest of the circuit? Use the [Ohm's & Watt's Law Calculator]({{ "/tools/ohms-law-calculator/" | relative_url }}).

## How FieldPad helps in the field

Size the run here, then keep the numbers with the job. **FieldPad** logs your electrical readings and corrections against the equipment and the client, documents the fix, and turns it into a signed estimate or invoice — offline, on your iPhone.
