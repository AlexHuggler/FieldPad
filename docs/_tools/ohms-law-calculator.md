---
title: "Ohm's Law & Watt's Law Calculator"
short_title: "Ohm's & Watt's Law Calculator"
description: "Free Ohm's law and Watt's law calculator. Enter any two of voltage, current, resistance, and power and instantly solve the other two — for HVAC techs, electricians, and students."
card_blurb: "Enter any two of volts, amps, ohms, and watts to solve the other two via Ohm's and Watt's law."
tool_category: "Electrical"
tool_order: 2
keywords: ["ohms law calculator", "watts law calculator", "volts amps ohms watts", "electrical formula calculator", "power calculator hvac"]
formula_summary: "Ohm's law V = I × R and Watt's law P = V × I, plus P = I²R and P = V²/R and all rearrangements. Enter any two knowns to solve the rest."
how_to_name: "How to use Ohm's law and Watt's law"
how_to_steps:
  - { name: "Enter any two values", text: "Fill in any two of voltage (V), current (I), resistance (R), or power (P). Leave the other two blank." }
  - { name: "Solve", text: "The calculator applies Ohm's law and Watt's law to compute the remaining two values." }
  - { name: "Apply it", text: "Use the result to size components, check a motor's draw, or verify a circuit. Note these assume a resistive, in-phase load." }
faqs:
  - { q: "What is Ohm's law?", a: "Ohm's law states that voltage equals current times resistance: V = I × R. Rearranged, I = V ÷ R and R = V ÷ I. It relates the voltage across a component, the current through it, and its resistance." }
  - { q: "What is Watt's law?", a: "Watt's law relates power to voltage and current: P = V × I. Combined with Ohm's law it also gives P = I² × R and P = V² ÷ R, so you can find power from any two of voltage, current, and resistance." }
  - { q: "Can I find watts from volts and amps?", a: "Yes. Power in watts = volts × amps (P = V × I). For example, 240 V × 10 A = 2,400 W. This calculator does that and every other Ohm's/Watt's law combination from any two known values." }
  - { q: "Does Ohm's law work for AC motor circuits?", a: "Ohm's and Watt's law as shown assume a resistive, in-phase load. Motors and other inductive loads have a power factor, so real power is V × I × power factor. Use these results as a baseline and account for power factor on motor circuits, or consult a licensed electrician." }
---

<div class="not-prose bg-white border border-gray-200 rounded-2xl p-6 my-8 shadow-sm">
  <p class="text-sm text-gray-500 mb-4">Enter <strong>any two</strong> values and leave the rest blank.</p>
  <form id="ol-form" class="grid grid-cols-1 sm:grid-cols-2 gap-5">
    <div>
      <label class="calc-label" for="ol-v">Voltage (V)</label>
      <input class="calc-field" type="number" inputmode="decimal" step="any" id="ol-v" placeholder="volts">
    </div>
    <div>
      <label class="calc-label" for="ol-i">Current (A)</label>
      <input class="calc-field" type="number" inputmode="decimal" step="any" id="ol-i" placeholder="amps">
    </div>
    <div>
      <label class="calc-label" for="ol-r">Resistance (Ω)</label>
      <input class="calc-field" type="number" inputmode="decimal" step="any" id="ol-r" placeholder="ohms">
    </div>
    <div>
      <label class="calc-label" for="ol-p">Power (W)</label>
      <input class="calc-field" type="number" inputmode="decimal" step="any" id="ol-p" placeholder="watts">
    </div>
    <div class="sm:col-span-2 flex flex-wrap gap-3 items-center">
      <button class="calc-btn" type="submit">Solve</button>
      <button class="text-sm text-gray-500 hover:text-fieldpad-700" type="reset">Reset</button>
    </div>
  </form>
  <div id="ol-result" class="calc-result mt-5" aria-live="polite"></div>
</div>

<script>
(function () {
  var form = document.getElementById('ol-form');
  var out  = document.getElementById('ol-result');
  if (!form) return;
  function round(n) { return Math.round(n * 1000) / 1000; }
  form.addEventListener('submit', function (e) {
    e.preventDefault();
    var V = parseFloat(document.getElementById('ol-v').value);
    var I = parseFloat(document.getElementById('ol-i').value);
    var R = parseFloat(document.getElementById('ol-r').value);
    var P = parseFloat(document.getElementById('ol-p').value);
    var known = [['V', V], ['I', I], ['R', R], ['P', P]].filter(function (k) { return !isNaN(k[1]); });
    if (known.length < 2) {
      out.innerHTML = '<span class="calc-status calc-status-warn">Enter at least two values.</span>';
      return;
    }
    var hV = !isNaN(V), hI = !isNaN(I), hR = !isNaN(R), hP = !isNaN(P);
    // Resolve V, I, R, P from any two
    if (hV && hI) { if (!hR) R = (I !== 0 ? V / I : NaN); if (!hP) P = V * I; }
    else if (hV && hR) { if (!hI) I = (R !== 0 ? V / R : NaN); P = V * I; }
    else if (hV && hP) { if (!hI) I = (V !== 0 ? P / V : NaN); R = (I !== 0 ? V / I : NaN); }
    else if (hI && hR) { V = I * R; P = V * I; }
    else if (hI && hP) { if (!hV) V = (I !== 0 ? P / I : NaN); R = (I !== 0 ? V / I : NaN); }
    else if (hR && hP) { if (!hV) V = Math.sqrt(P * R); I = (R !== 0 ? V / R : NaN); }
    var bad = [V, I, R, P].some(function (x) { return isNaN(x) || !isFinite(x); });
    if (bad) {
      out.innerHTML = '<span class="calc-status calc-status-warn">Those values can\'t be solved (check for a zero where a divisor is needed).</span>';
      return;
    }
    out.innerHTML =
      '<div class="grid grid-cols-2 gap-2">' +
      '<div>Voltage: <strong>' + round(V) + ' V</strong></div>' +
      '<div>Current: <strong>' + round(I) + ' A</strong></div>' +
      '<div>Resistance: <strong>' + round(R) + ' &Omega;</strong></div>' +
      '<div>Power: <strong>' + round(P) + ' W</strong></div>' +
      '</div>' +
      '<span class="calc-status calc-status-ok">Assumes a resistive, in-phase load. For motors and inductive loads, apply power factor (real power = V × I × PF).</span>';
  });
  form.addEventListener('reset', function () { out.innerHTML = ''; });
})();
</script>

## How Ohm's law and Watt's law work

**Ohm's law** ties voltage, current, and resistance together:

**V = I × R** (so **I = V ÷ R** and **R = V ÷ I**)

**Watt's law** brings in power:

**P = V × I** — and combined with Ohm's law, **P = I² × R** and **P = V² ÷ R**.

Between them, any **two** known values let you solve the other two. The wheel of relationships:

| Find | From V & I | From V & R | From I & R | From P & … |
|---|---|---|---|---|
| V | — | — | I × R | √(P × R) |
| I | — | V ÷ R | — | P ÷ V |
| R | V ÷ I | — | — | V² ÷ P |
| P | V × I | V² ÷ R | I² × R | — |

### Worked example

A condenser fan motor draws **2.5 A** at **240 V**:

- Power = 240 × 2.5 = **600 W**
- Resistance (apparent) = 240 ÷ 2.5 = **96 Ω**

## A note on motors

These formulas assume a **resistive, in-phase load**. Motors and other inductive loads have a **power factor**, so true power is `V × I × PF`. Use these results as a baseline for resistive elements (heat strips, ignitors), and account for power factor on motor circuits.

> Sizing a conductor run? Pair this with the [Voltage Drop Calculator]({{ "/tools/voltage-drop-calculator/" | relative_url }}).

## How FieldPad helps in the field

Solve the circuit here, then keep the readings with the work. **FieldPad** logs electrical measurements against the equipment and the job, documents the failed component, and turns the repair into a signed estimate or invoice — offline, on your iPhone, with full history on one record.
