---
title: "Fan Laws Calculator"
short_title: "Fan Laws Calculator"
description: "Free fan laws (affinity laws) calculator for HVAC blowers. Enter original RPM, CFM, static pressure, and BHP plus a new RPM to see how airflow, static pressure, and motor power change."
card_blurb: "See how blower CFM, static pressure, and brake horsepower change with RPM using the three fan laws."
tool_category: "Airflow"
tool_order: 5
keywords: ["fan laws calculator", "affinity laws hvac", "blower rpm cfm", "fan law static pressure", "fan brake horsepower rpm"]
formula_summary: "CFM₂ = CFM₁ × (RPM₂/RPM₁); SP₂ = SP₁ × (RPM₂/RPM₁)²; BHP₂ = BHP₁ × (RPM₂/RPM₁)³."
how_to_name: "How to apply the fan laws"
how_to_steps:
  - { name: "Record the original operating point", text: "Note the current blower RPM, airflow (CFM), static pressure, and brake horsepower." }
  - { name: "Enter the new RPM", text: "Enter the RPM you plan to change to (via pulley adjustment or speed tap/VFD)." }
  - { name: "Read the projected results", text: "CFM scales linearly with RPM, static pressure with RPM squared, and brake horsepower with RPM cubed." }
  - { name: "Check the motor", text: "Because power scales with the cube of RPM, verify the motor's amp and thermal rating before increasing speed." }
faqs:
  - { q: "What are the three fan laws?", a: "Fan Law 1: airflow is proportional to RPM (CFM₂ = CFM₁ × RPM₂/RPM₁). Fan Law 2: static pressure is proportional to RPM squared. Fan Law 3: brake horsepower is proportional to RPM cubed. They describe how a fan's output changes when its speed changes." }
  - { q: "If I increase blower RPM by 10%, what happens?", a: "Airflow rises about 10%, static pressure rises about 21% (1.1²), and brake horsepower rises about 33% (1.1³). The cubic jump in power is why even small speed increases can overload a motor." }
  - { q: "Why does horsepower go up so fast with fan speed?", a: "Brake horsepower scales with the cube of the speed ratio. Doubling RPM doubles airflow but raises static pressure 4× and power draw 8×. Always confirm the motor's amp draw and rating before raising fan speed." }
  - { q: "Do the fan laws account for my ductwork?", a: "They assume a constant system curve (the ductwork resistance doesn't change). In a real system, resistance rises with airflow, so the actual CFM gain is usually a bit less than the fan laws predict. Use them as a close estimate, then verify with measured airflow and static pressure." }
---

<div class="not-prose bg-white border border-gray-200 rounded-2xl p-6 my-8 shadow-sm">
  <form id="fl-form" class="grid grid-cols-1 sm:grid-cols-2 gap-5">
    <div>
      <label class="calc-label" for="fl-rpm1">Original RPM</label>
      <input class="calc-field" type="number" inputmode="decimal" step="any" id="fl-rpm1" placeholder="e.g. 800">
    </div>
    <div>
      <label class="calc-label" for="fl-rpm2">New RPM</label>
      <input class="calc-field" type="number" inputmode="decimal" step="any" id="fl-rpm2" placeholder="e.g. 900">
    </div>
    <div>
      <label class="calc-label" for="fl-cfm1">Original CFM (optional)</label>
      <input class="calc-field" type="number" inputmode="decimal" step="any" id="fl-cfm1" placeholder="e.g. 1000">
    </div>
    <div>
      <label class="calc-label" for="fl-sp1">Original static pressure (optional)</label>
      <input class="calc-field" type="number" inputmode="decimal" step="any" id="fl-sp1" placeholder="e.g. 0.5">
    </div>
    <div>
      <label class="calc-label" for="fl-bhp1">Original BHP (optional)</label>
      <input class="calc-field" type="number" inputmode="decimal" step="any" id="fl-bhp1" placeholder="e.g. 0.33">
    </div>
    <div class="sm:col-span-2 flex flex-wrap gap-3 items-center">
      <button class="calc-btn" type="submit">Apply fan laws</button>
      <button class="text-sm text-gray-500 hover:text-fieldpad-700" type="reset">Reset</button>
    </div>
  </form>
  <div id="fl-result" class="calc-result mt-5" aria-live="polite"></div>
</div>

<script>
(function () {
  var form = document.getElementById('fl-form');
  var out  = document.getElementById('fl-result');
  if (!form) return;
  function round(n, d) { var f = Math.pow(10, d || 2); return Math.round(n * f) / f; }
  form.addEventListener('submit', function (e) {
    e.preventDefault();
    var r1 = parseFloat(document.getElementById('fl-rpm1').value);
    var r2 = parseFloat(document.getElementById('fl-rpm2').value);
    var cfm = parseFloat(document.getElementById('fl-cfm1').value);
    var sp  = parseFloat(document.getElementById('fl-sp1').value);
    var bhp = parseFloat(document.getElementById('fl-bhp1').value);
    if (isNaN(r1) || isNaN(r2) || r1 <= 0) {
      out.innerHTML = '<span class="calc-status calc-status-warn">Enter both the original and new RPM.</span>';
      return;
    }
    var ratio = r2 / r1;
    var html = 'RPM ratio: <span class="calc-result-value">' + round(ratio, 3) + '×</span>' +
      '<div class="mt-2">Airflow: ' + round((ratio - 1) * 100, 1) + '% &rarr; ' + (isNaN(cfm) ? '' : '<strong>' + Math.round(cfm * ratio).toLocaleString() + ' CFM</strong>') + '</div>' +
      '<div>Static pressure: ' + round((ratio * ratio - 1) * 100, 1) + '% &rarr; ' + (isNaN(sp) ? '' : '<strong>' + round(sp * ratio * ratio, 3) + '"</strong>') + '</div>' +
      '<div>Brake horsepower: ' + round((Math.pow(ratio, 3) - 1) * 100, 1) + '% &rarr; ' + (isNaN(bhp) ? '' : '<strong>' + round(bhp * Math.pow(ratio, 3), 3) + ' BHP</strong>') + '</div>';
    if (ratio > 1.05) {
      html += '<span class="calc-status calc-status-warn">Power rises with the cube of speed — verify the motor\'s amp draw and rating before increasing RPM.</span>';
    } else {
      html += '<span class="calc-status calc-status-ok">Projection assumes a constant system curve. Verify with measured airflow and static pressure.</span>';
    }
    out.innerHTML = html;
  });
  form.addEventListener('reset', function () { out.innerHTML = ''; });
})();
</script>

## How the fan laws work

The **fan laws** (affinity laws) describe how a blower's output changes when you change its speed — by swapping a pulley, changing a belt, or moving a speed tap or VFD setting:

| Law | Relationship |
|---|---|
| **1. Airflow** | CFM₂ = CFM₁ × (RPM₂ ÷ RPM₁) |
| **2. Static pressure** | SP₂ = SP₁ × (RPM₂ ÷ RPM₁)² |
| **3. Brake horsepower** | BHP₂ = BHP₁ × (RPM₂ ÷ RPM₁)³ |

### Worked example

Raise a blower from **800 to 900 RPM** (ratio 1.125):

- Airflow: 1,000 CFM × 1.125 = **1,125 CFM** (+12.5%)
- Static pressure: × 1.125² = **+27%**
- Brake horsepower: × 1.125³ = **+42%**

A modest speed bump nearly halves the headroom on the motor.

## The catch: power scales with the cube

Because **brake horsepower follows the cube of the speed ratio**, doubling RPM doubles airflow but raises power draw **8×**. Always verify the motor's amp draw and nameplate rating before raising fan speed — and remember these laws assume a **constant system curve**, so real-world CFM gains run a little under the prediction as duct resistance rises.

> Use this with the [CFM & Tonnage Calculator]({{ "/tools/cfm-tonnage-calculator/" | relative_url }}) to target the right airflow, and the [Static Pressure Calculator]({{ "/tools/static-pressure-calculator/" | relative_url }}) to check the result.

## How FieldPad helps in the field

Project the change here, then log the before-and-after readings in **FieldPad** against the equipment and the job. Keep your blower-setup notes on the same record as the invoice, and turn airflow corrections into client-ready estimates — offline, on your iPhone.
