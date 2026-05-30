---
title: "Run Capacitor (MFD) Calculator"
short_title: "Capacitor (MFD) Calculator"
description: "Free run capacitor calculator for HVAC techs. Measure the start-winding amps and run voltage to calculate a motor's capacitor value in microfarads (µF/MFD) and compare it to the rated value."
card_blurb: "Calculate a run capacitor's microfarad (µF) value from measured amps and voltage, and check it against the rating."
tool_category: "Electrical"
tool_order: 1
keywords: ["capacitor calculator", "MFD calculator", "run capacitor microfarad", "hvac capacitor test", "how to test a capacitor with amps", "capacitor amps formula"]
formula_summary: "Capacitance (µF) = (2652 × Amps) ÷ Volts, measuring start-winding amps and run voltage at 60 Hz."
how_to_name: "How to calculate a run capacitor's microfarads from amps and volts"
how_to_steps:
  - { name: "Measure the start-winding current", text: "With the motor running, clamp your ammeter on the wire feeding the start winding through the capacitor (the lead between the capacitor terminal and the start winding)." }
  - { name: "Measure the voltage across the capacitor", text: "Measure the AC voltage across the capacitor terminals while the motor runs." }
  - { name: "Apply the formula", text: "Microfarads = (2652 × measured amps) ÷ measured volts. The 2652 constant assumes 60 Hz line frequency." }
  - { name: "Compare to the rating", text: "Compare the calculated µF to the capacitor's rated value. A run capacitor is generally acceptable within ±6% of its rating." }
faqs:
  - { q: "How do you calculate a capacitor's microfarads with amps and volts?", a: "Microfarads (µF) = (2652 × amps) ÷ volts. Clamp the start-winding amps and measure the voltage across the capacitor while the motor runs, then apply the formula. The 2652 constant is derived from the 60 Hz line frequency (2652 = 1,000,000 ÷ (2π × 60))." }
  - { q: "What is the acceptable tolerance for a run capacitor?", a: "A run capacitor is generally considered good if its measured value is within ±6% of the rated microfarads printed on the can. Outside that range — especially below it — the capacitor is weak and should be replaced." }
  - { q: "Why is the constant 2652?", a: "Capacitance in microfarads equals (amps ÷ volts) ÷ (2π × frequency) × 1,000,000. At 60 Hz, 1,000,000 ÷ (2 × π × 60) ≈ 2652, so µF = 2652 × amps ÷ volts. On a 50 Hz system the constant is about 3183." }
  - { q: "Can I test a capacitor with a multimeter instead?", a: "Yes — a meter with a capacitance (µF) range can read a disconnected, discharged capacitor directly. The amps-and-volts method is useful because it tests the capacitor under real running load without removing it, but always discharge a capacitor safely before handling it." }
---

<div class="not-prose bg-white border border-gray-200 rounded-2xl p-6 my-8 shadow-sm">
  <form id="cap-form" class="grid grid-cols-1 sm:grid-cols-2 gap-5">
    <div>
      <label class="calc-label" for="cap-amps">Start-winding current (amps)</label>
      <input class="calc-field" type="number" inputmode="decimal" step="any" id="cap-amps" placeholder="e.g. 8.3">
    </div>
    <div>
      <label class="calc-label" for="cap-volts">Voltage across capacitor (V)</label>
      <input class="calc-field" type="number" inputmode="decimal" step="any" id="cap-volts" placeholder="e.g. 240">
    </div>
    <div>
      <label class="calc-label" for="cap-rated">Rated capacitance (µF, optional)</label>
      <input class="calc-field" type="number" inputmode="decimal" step="any" id="cap-rated" placeholder="e.g. 45">
    </div>
    <div>
      <label class="calc-label" for="cap-freq">Line frequency</label>
      <select class="calc-field" id="cap-freq">
        <option value="2652">60 Hz</option>
        <option value="3183">50 Hz</option>
      </select>
    </div>
    <div class="sm:col-span-2 flex flex-wrap gap-3 items-center">
      <button class="calc-btn" type="submit">Calculate µF</button>
      <button class="text-sm text-gray-500 hover:text-fieldpad-700" type="reset">Reset</button>
    </div>
  </form>
  <div id="cap-result" class="calc-result mt-5" aria-live="polite"></div>
</div>

<script>
(function () {
  var form = document.getElementById('cap-form');
  var out  = document.getElementById('cap-result');
  if (!form) return;
  form.addEventListener('submit', function (e) {
    e.preventDefault();
    var amps  = parseFloat(document.getElementById('cap-amps').value);
    var volts = parseFloat(document.getElementById('cap-volts').value);
    var rated = parseFloat(document.getElementById('cap-rated').value);
    var k     = parseFloat(document.getElementById('cap-freq').value);
    if (isNaN(amps) || isNaN(volts) || volts === 0) {
      out.innerHTML = '<span class="calc-status calc-status-warn">Enter the start-winding amps and a non-zero voltage.</span>';
      return;
    }
    var uf = (k * amps) / volts;
    var html = 'Measured capacitance: <span class="calc-result-value">' + uf.toFixed(1) + ' &micro;F</span>';
    if (!isNaN(rated) && rated > 0) {
      var pct = ((uf - rated) / rated) * 100;
      var sign = pct >= 0 ? '+' : '';
      if (Math.abs(pct) <= 6) {
        html += '<span class="calc-status calc-status-ok">' + sign + pct.toFixed(1) + '% of the ' + rated + ' &micro;F rating — within the &plusmn;6% tolerance. Capacitor is good.</span>';
      } else if (pct < 0) {
        html += '<span class="calc-status calc-status-alert">' + pct.toFixed(1) + '% of the ' + rated + ' &micro;F rating — weak/failing capacitor. Replace it.</span>';
      } else {
        html += '<span class="calc-status calc-status-warn">' + sign + pct.toFixed(1) + '% of the ' + rated + ' &micro;F rating — outside &plusmn;6%. Recheck your readings and consider replacement.</span>';
      }
    } else {
      html += '<span class="calc-status calc-status-ok">Compare this to the rating on the capacitor can — a run capacitor is generally good within &plusmn;6%.</span>';
    }
    out.innerHTML = html;
  });
  form.addEventListener('reset', function () { out.innerHTML = ''; });
})();
</script>

## How the capacitor calculation works

You can test a run capacitor **under load**, without removing it, by measuring the current through the start winding and the voltage across the capacitor while the motor runs:

**Capacitance (µF) = (2652 × Amps) ÷ Volts**

The **2652** constant comes from the 60 Hz line frequency: capacitance equals `(amps ÷ volts) ÷ (2π × f) × 1,000,000`, and at 60 Hz `1,000,000 ÷ (2π × 60) ≈ 2652`. On a 50 Hz system the constant is about **3183**.

### Worked example

You clamp **8.3 A** on the start-winding lead and read **240 V** across the capacitor:

**(2652 × 8.3) ÷ 240 = 22,012 ÷ 240 ≈ 91.7 µF**

If the can is rated 90 µF, that's about +1.9% — well within tolerance, so the capacitor is good.

## Reading the result

A run capacitor is generally considered **good within ±6%** of its rated microfarads:

| Measured vs. rated | Verdict |
|---|---|
| Within ±6% | Good |
| More than 6% below | Weak / failing — replace |
| More than 6% above | Recheck readings; consider replacement |

> Always **discharge a capacitor safely** before handling it, and observe proper lockout and PPE. A meter with a capacitance range can also read a disconnected, discharged capacitor directly — the amps-and-volts method is handy because it tests under real running conditions.

## How FieldPad helps in the field

Run the check here, then log the result in **FieldPad** against the equipment and the visit. Note the failed part, attach it to the job, and turn the repair into a signed estimate or invoice with the capacitor as a line item — offline, on your iPhone, with the full service history on the same record.
