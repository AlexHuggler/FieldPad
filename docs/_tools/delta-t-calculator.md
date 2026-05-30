---
title: "Delta T (Temperature Split) Calculator"
short_title: "Delta T Calculator"
description: "Free Delta T calculator for HVAC techs. Enter return-air and supply-air temperatures to get the temperature split across the evaporator coil, with normal-range guidance."
card_blurb: "Calculate the temperature split (Delta T) across the evaporator coil from return and supply air temperatures."
tool_category: "Airflow"
tool_order: 1
keywords: ["delta t calculator", "temperature split calculator", "hvac delta t", "evaporator delta t", "supply return temperature split"]
formula_summary: "Delta T = Return Air Temperature − Supply Air Temperature (dry-bulb, across the evaporator coil)."
how_to_name: "How to measure Delta T across the coil"
how_to_steps:
  - { name: "Measure return-air temperature", text: "Take the dry-bulb temperature in the return duct, before the evaporator coil." }
  - { name: "Measure supply-air temperature", text: "Take the dry-bulb temperature in the supply duct, downstream of the coil but before it picks up duct heat gain." }
  - { name: "Subtract", text: "Delta T = return-air temperature − supply-air temperature." }
  - { name: "Compare to the normal range", text: "A typical cooling split is about 16–22°F. Outside that range points to an airflow or charge problem." }
faqs:
  - { q: "What is a normal Delta T for an AC system?", a: "For a typical residential cooling system, the supply-to-return temperature split is usually about 16–22°F. The exact normal value depends on the indoor humidity (wet bulb) — drier indoor air tends toward the high end, humid indoor air toward the low end." }
  - { q: "How do you calculate Delta T?", a: "Delta T = return-air dry-bulb temperature − supply-air dry-bulb temperature. Measure both in the ductwork close to the coil and subtract." }
  - { q: "What does a low Delta T mean?", a: "A low temperature split (below ~16°F) often means too much airflow for the load, low refrigerant charge, or a dirty/failing evaporator. The coil isn't removing enough heat from each pound of air." }
  - { q: "What does a high Delta T mean?", a: "A high split (above ~22°F) usually means too little airflow — a dirty filter, undersized or blocked ducts, a slipping or slow blower, or a dirty evaporator coil. Air is moving too slowly across the coil." }
---

<div class="not-prose bg-white border border-gray-200 rounded-2xl p-6 my-8 shadow-sm">
  <form id="dt-form" class="grid grid-cols-1 sm:grid-cols-2 gap-5">
    <div>
      <label class="calc-label" for="dt-return">Return-air temperature (°F)</label>
      <input class="calc-field" type="number" inputmode="decimal" step="any" id="dt-return" placeholder="e.g. 75">
    </div>
    <div>
      <label class="calc-label" for="dt-supply">Supply-air temperature (°F)</label>
      <input class="calc-field" type="number" inputmode="decimal" step="any" id="dt-supply" placeholder="e.g. 56">
    </div>
    <div class="sm:col-span-2 flex flex-wrap gap-3 items-center">
      <button class="calc-btn" type="submit">Calculate Delta T</button>
      <button class="text-sm text-gray-500 hover:text-fieldpad-700" type="reset">Reset</button>
    </div>
  </form>
  <div id="dt-result" class="calc-result mt-5" aria-live="polite"></div>
</div>

<script>
(function () {
  var form = document.getElementById('dt-form');
  var out  = document.getElementById('dt-result');
  if (!form) return;
  form.addEventListener('submit', function (e) {
    e.preventDefault();
    var ret = parseFloat(document.getElementById('dt-return').value);
    var sup = parseFloat(document.getElementById('dt-supply').value);
    if (isNaN(ret) || isNaN(sup)) {
      out.innerHTML = '<span class="calc-status calc-status-warn">Enter both the return-air and supply-air temperatures.</span>';
      return;
    }
    var dt = ret - sup;
    var html = 'Temperature split (Delta T): <span class="calc-result-value">' + dt.toFixed(1) + ' &deg;F</span>';
    if (dt < 16) {
      html += '<span class="calc-status calc-status-warn">Below the typical 16–22&deg;F range — possible too much airflow for the load, low charge, or a failing evaporator.</span>';
    } else if (dt <= 22) {
      html += '<span class="calc-status calc-status-ok">In the typical 16–22&deg;F cooling range.</span>';
    } else {
      html += '<span class="calc-status calc-status-alert">Above the typical 16–22&deg;F range — usually too little airflow: dirty filter, restricted ducts, slow blower, or a dirty coil.</span>';
    }
    out.innerHTML = html;
  });
  form.addEventListener('reset', function () { out.innerHTML = ''; });
})();
</script>

## How the Delta T calculation works

**Delta T** (the temperature split) is the difference between the air entering the evaporator coil (return) and the air leaving it (supply). It's one of the fastest checks of whether an air conditioning system is moving the right amount of air across a properly performing coil.

**Delta T (°F) = Return-Air Temperature − Supply-Air Temperature**

### Worked example

Return air measures **75°F** and supply air measures **56°F**:

**75 − 56 = 19°F split** — right in the normal cooling range.

## Reading the result

A typical residential cooling split runs about **16–22°F**. The "right" number within that band depends on indoor humidity — the drier the indoor air (lower wet bulb), the higher the expected split.

| Split | Likely cause |
|---|---|
| Low (under ~16°F) | Too much airflow for the load, low refrigerant charge, or a failing/iced evaporator |
| Normal (16–22°F) | Airflow and capacity reasonably matched |
| High (over ~22°F) | Too little airflow — dirty filter, restricted ducts, slow blower, or dirty coil |

> Delta T is a screening check, not a charging method. Pair it with [superheat]({{ "/tools/superheat-calculator/" | relative_url }}), [subcooling]({{ "/tools/subcooling-calculator/" | relative_url }}), and a real [airflow (CFM)]({{ "/tools/cfm-tonnage-calculator/" | relative_url }}) measurement before condemning a component.

## How FieldPad helps in the field

Take the split here, then log it in **FieldPad** against the equipment and the visit. Trend it across maintenance calls, attach it to the job, and turn the diagnosis into an estimate — all on one offline-ready record that travels with the client.
