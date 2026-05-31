---
title: "Furnace Temperature Rise & Airflow Calculator"
short_title: "Furnace Temp Rise Calculator"
description: "Free furnace temperature rise and airflow calculator. Find temperature rise from supply and return temps, and calculate heating CFM from furnace BTU output and the measured rise."
card_blurb: "Find furnace temperature rise and the resulting heating CFM from BTU output and supply/return temperatures."
tool_category: "Heating & Gas"
tool_order: 1
keywords: ["furnace temperature rise calculator", "heating cfm calculator", "furnace airflow", "temp rise gas furnace", "btu output cfm heating"]
formula_summary: "Temperature rise = supply temp − return temp. Heating CFM = BTU output ÷ (1.08 × temp rise), where output = input × efficiency."
how_to_name: "How to calculate furnace temperature rise and airflow"
how_to_steps:
  - { name: "Measure supply and return temps", text: "Measure the supply-air and return-air dry-bulb temperatures with the furnace at steady-state heating (avoid radiant heat from the heat exchanger on the supply probe)." }
  - { name: "Subtract for the rise", text: "Temperature rise = supply temperature − return temperature." }
  - { name: "Find the BTU output", text: "Output = furnace input (BTU/h) × efficiency (e.g. 80% AFUE → 0.80). Use OUTPUT, not input." }
  - { name: "Solve for CFM", text: "Heating CFM = BTU output ÷ (1.08 × temperature rise). Compare the rise to the nameplate rise range." }
faqs:
  - { q: "What is a normal furnace temperature rise?", a: "Most gas furnaces have a nameplate temperature rise range printed on the rating plate, typically a 30–40°F window somewhere between about 35°F and 85°F (for example, 45–75°F). You want the measured rise to land within that range, ideally near the middle." }
  - { q: "How do you calculate furnace airflow (CFM)?", a: "Heating CFM = BTU output ÷ (1.08 × temperature rise). The BTU output is the furnace input multiplied by its efficiency — for example, a 100,000 BTU/h input at 80% AFUE is 80,000 BTU/h output. The 1.08 factor is for standard air." }
  - { q: "Do I use BTU input or output for the airflow formula?", a: "Use BTU output — the heat actually delivered to the airstream. Output = input × efficiency. Using the input value would overstate the airflow because not all the input energy reaches the supply air." }
  - { q: "What does it mean if the temperature rise is too high or too low?", a: "A rise above the nameplate maximum means airflow is too low (dirty filter, undersized or closed ducts, slow blower) and can crack a heat exchanger. A rise below the minimum means airflow is too high (oversized blower, return leaks) and can cause condensation and short cycling." }
---

<div class="not-prose bg-white border border-gray-200 rounded-2xl p-6 my-8 shadow-sm">
  <form id="tr-form" class="grid grid-cols-1 sm:grid-cols-2 gap-5">
    <div>
      <label class="calc-label" for="tr-supply">Supply-air temperature (°F)</label>
      <input class="calc-field" type="number" inputmode="decimal" step="any" id="tr-supply" placeholder="e.g. 130">
    </div>
    <div>
      <label class="calc-label" for="tr-return">Return-air temperature (°F)</label>
      <input class="calc-field" type="number" inputmode="decimal" step="any" id="tr-return" placeholder="e.g. 70">
    </div>
    <div>
      <label class="calc-label" for="tr-input">Furnace input (BTU/h)</label>
      <input class="calc-field" type="number" inputmode="decimal" step="any" id="tr-input" placeholder="e.g. 100000">
    </div>
    <div>
      <label class="calc-label" for="tr-eff">Efficiency / AFUE (%)</label>
      <input class="calc-field" type="number" inputmode="decimal" step="any" id="tr-eff" placeholder="e.g. 80">
    </div>
    <div class="sm:col-span-2 flex flex-wrap gap-3 items-center">
      <button class="calc-btn" type="submit">Calculate</button>
      <button class="text-sm text-gray-500 hover:text-fieldpad-700" type="reset">Reset</button>
    </div>
  </form>
  <div id="tr-result" class="calc-result mt-5" aria-live="polite"></div>
</div>

<script>
(function () {
  var form = document.getElementById('tr-form');
  var out  = document.getElementById('tr-result');
  if (!form) return;
  form.addEventListener('submit', function (e) {
    e.preventDefault();
    var sup = parseFloat(document.getElementById('tr-supply').value);
    var ret = parseFloat(document.getElementById('tr-return').value);
    if (isNaN(sup) || isNaN(ret)) {
      out.innerHTML = '<span class="calc-status calc-status-warn">Enter the supply and return temperatures.</span>';
      return;
    }
    var rise = sup - ret;
    var html = 'Temperature rise: <span class="calc-result-value">' + rise.toFixed(1) + ' &deg;F</span>';
    var inp = parseFloat(document.getElementById('tr-input').value);
    var eff = parseFloat(document.getElementById('tr-eff').value);
    if (!isNaN(inp) && !isNaN(eff) && rise > 0) {
      var output = inp * (eff / 100);
      var cfm = output / (1.08 * rise);
      html += '<div class="mt-2">BTU output: <strong>' + Math.round(output).toLocaleString() + ' BTU/h</strong></div>' +
              '<div>Heating airflow: <strong>' + Math.round(cfm).toLocaleString() + ' CFM</strong></div>';
    } else if (rise <= 0) {
      html += '<span class="calc-status calc-status-warn">Supply should be warmer than return in heating — check your probe placement.</span>';
    }
    if (rise > 0) {
      if (rise < 30) html += '<span class="calc-status calc-status-warn">Below a typical nameplate minimum — airflow may be too high (oversized blower, return leaks). Confirm against the rating plate range.</span>';
      else if (rise <= 70) html += '<span class="calc-status calc-status-ok">Within a typical nameplate rise range. Confirm the exact range on the furnace rating plate.</span>';
      else html += '<span class="calc-status calc-status-alert">High rise — airflow may be too low (dirty filter, restricted ducts, slow blower). This can overheat the heat exchanger. Confirm against the rating plate.</span>';
    }
    out.innerHTML = html;
  });
  form.addEventListener('reset', function () { out.innerHTML = ''; });
})();
</script>

## How the temperature rise & airflow calculation works

**Temperature rise** is how much the furnace heats the air as it passes through:

**Temperature Rise (°F) = Supply Temperature − Return Temperature**

Every furnace has a **nameplate rise range** (a roughly 30–40°F window, e.g. 45–75°F). The measured rise should land inside it — ideally near the middle.

Once you know the rise and the furnace's heat output, you can solve for the airflow it's actually moving:

**Heating CFM = BTU Output ÷ (1.08 × Temperature Rise)**

where **BTU Output = Input × Efficiency** (e.g. 100,000 BTU/h input × 0.80 AFUE = 80,000 BTU/h output). Always use **output**, not input — and the **1.08** factor assumes standard air.

### Worked example

A furnace with **100,000 BTU/h** input at **80% AFUE** measures **130°F** supply and **70°F** return:

- Rise = 130 − 70 = **60°F** (within a typical range)
- Output = 100,000 × 0.80 = **80,000 BTU/h**
- CFM = 80,000 ÷ (1.08 × 60) ≈ **1,235 CFM**

## Reading the rise

| Rise vs. nameplate | Meaning |
|---|---|
| Below minimum | Airflow too high — oversized blower, return leaks, condensation risk |
| Within range | Airflow matched to the furnace |
| Above maximum | Airflow too low — dirty filter, restricted ducts, slow blower; can crack the heat exchanger |

> This is the heating-mode counterpart to the [CFM & Tonnage Calculator]({{ "/tools/cfm-tonnage-calculator/" | relative_url }}). Confirm restrictions with the [Static Pressure Calculator]({{ "/tools/static-pressure-calculator/" | relative_url }}).

## How FieldPad helps in the field

Run the rise and airflow here, then log them in **FieldPad** against the furnace and the client. Keep the readings on the same record as the maintenance job, flag a heat-exchanger concern, and turn it into a documented estimate — offline, on your iPhone.
