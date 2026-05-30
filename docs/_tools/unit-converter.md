---
title: "HVAC Unit Converter"
short_title: "HVAC Unit Converter"
description: "Free HVAC unit converter for technicians. Convert tons, BTU/h, and kW; Fahrenheit and Celsius; SEER, EER, and COP; PSI and kPa; and pounds and kilograms — all in one place."
card_blurb: "Convert tons/BTU/kW, °F/°C, SEER/EER/COP, PSI/kPa, and lb/kg — the everyday HVAC unit conversions in one place."
tool_category: "Conversions"
tool_order: 1
keywords: ["hvac unit converter", "tons to btu", "btu to kw", "seer to eer", "eer to cop", "psi to kpa", "fahrenheit to celsius hvac"]
formula_summary: "1 ton = 12,000 BTU/h = 3.517 kW. °F = °C × 9/5 + 32. COP = EER ÷ 3.412. 1 PSI = 6.895 kPa. 1 lb = 0.4536 kg."
how_to_name: "How to convert common HVAC units"
how_to_steps:
  - { name: "Pick the conversion", text: "Choose the unit pair you need — capacity, temperature, efficiency, pressure, or weight." }
  - { name: "Enter a value", text: "Type the known value into the field. Results update when you calculate." }
  - { name: "Read the converted result", text: "The tool applies the standard conversion factor and shows the equivalent value." }
faqs:
  - { q: "How many BTU are in a ton of cooling?", a: "One ton of refrigeration equals 12,000 BTU/h, which is approximately 3.517 kW. So a 2.5-ton system is 30,000 BTU/h." }
  - { q: "How do you convert EER to COP?", a: "COP = EER ÷ 3.412. The 3.412 factor converts BTU/h to watts. For example, an EER of 12 is a COP of about 3.52." }
  - { q: "How do you convert SEER to EER?", a: "There is no exact conversion because SEER is a seasonal average and EER is a single rating point, but a common field approximation is EER ≈ 0.875 × SEER (older rule) — manufacturer performance data is always preferred over any rule of thumb." }
  - { q: "How do you convert PSI to kPa?", a: "Multiply PSI by 6.895 to get kPa (1 PSI = 6.895 kPa). To go the other way, divide kPa by 6.895." }
---

<div class="not-prose bg-white border border-gray-200 rounded-2xl p-6 my-8 shadow-sm space-y-8">

  <!-- Capacity -->
  <div>
    <h3 class="text-base font-bold text-fieldpad-900 mb-4">Capacity: Tons &harr; BTU/h &harr; kW</h3>
    <form id="cap-conv" class="grid grid-cols-1 sm:grid-cols-3 gap-4 items-end">
      <div>
        <label class="calc-label" for="cap-val">Value</label>
        <input class="calc-field" type="number" inputmode="decimal" step="any" id="cap-val" placeholder="e.g. 3">
      </div>
      <div>
        <label class="calc-label" for="cap-from">From</label>
        <select class="calc-field" id="cap-from">
          <option value="tons">Tons</option>
          <option value="btu">BTU/h</option>
          <option value="kw">kW</option>
        </select>
      </div>
      <button class="calc-btn" type="submit">Convert</button>
    </form>
    <div id="cap-conv-result" class="calc-result mt-4" aria-live="polite"></div>
  </div>

  <hr class="border-gray-200">

  <!-- Temperature -->
  <div>
    <h3 class="text-base font-bold text-fieldpad-900 mb-4">Temperature: °F &harr; °C</h3>
    <form id="temp-conv" class="grid grid-cols-1 sm:grid-cols-3 gap-4 items-end">
      <div>
        <label class="calc-label" for="temp-val">Value</label>
        <input class="calc-field" type="number" inputmode="decimal" step="any" id="temp-val" placeholder="e.g. 72">
      </div>
      <div>
        <label class="calc-label" for="temp-from">From</label>
        <select class="calc-field" id="temp-from">
          <option value="f">Fahrenheit</option>
          <option value="c">Celsius</option>
        </select>
      </div>
      <button class="calc-btn" type="submit">Convert</button>
    </form>
    <div id="temp-conv-result" class="calc-result mt-4" aria-live="polite"></div>
  </div>

  <hr class="border-gray-200">

  <!-- Efficiency -->
  <div>
    <h3 class="text-base font-bold text-fieldpad-900 mb-4">Efficiency: EER &harr; COP</h3>
    <form id="eff-conv" class="grid grid-cols-1 sm:grid-cols-3 gap-4 items-end">
      <div>
        <label class="calc-label" for="eff-val">Value</label>
        <input class="calc-field" type="number" inputmode="decimal" step="any" id="eff-val" placeholder="e.g. 12">
      </div>
      <div>
        <label class="calc-label" for="eff-from">From</label>
        <select class="calc-field" id="eff-from">
          <option value="eer">EER</option>
          <option value="cop">COP</option>
        </select>
      </div>
      <button class="calc-btn" type="submit">Convert</button>
    </form>
    <div id="eff-conv-result" class="calc-result mt-4" aria-live="polite"></div>
  </div>

  <hr class="border-gray-200">

  <!-- Pressure -->
  <div>
    <h3 class="text-base font-bold text-fieldpad-900 mb-4">Pressure: PSI &harr; kPa</h3>
    <form id="pres-conv" class="grid grid-cols-1 sm:grid-cols-3 gap-4 items-end">
      <div>
        <label class="calc-label" for="pres-val">Value</label>
        <input class="calc-field" type="number" inputmode="decimal" step="any" id="pres-val" placeholder="e.g. 100">
      </div>
      <div>
        <label class="calc-label" for="pres-from">From</label>
        <select class="calc-field" id="pres-from">
          <option value="psi">PSI</option>
          <option value="kpa">kPa</option>
        </select>
      </div>
      <button class="calc-btn" type="submit">Convert</button>
    </form>
    <div id="pres-conv-result" class="calc-result mt-4" aria-live="polite"></div>
  </div>

  <hr class="border-gray-200">

  <!-- Weight -->
  <div>
    <h3 class="text-base font-bold text-fieldpad-900 mb-4">Weight: lb &harr; kg</h3>
    <form id="wt-conv" class="grid grid-cols-1 sm:grid-cols-3 gap-4 items-end">
      <div>
        <label class="calc-label" for="wt-val">Value</label>
        <input class="calc-field" type="number" inputmode="decimal" step="any" id="wt-val" placeholder="e.g. 25">
      </div>
      <div>
        <label class="calc-label" for="wt-from">From</label>
        <select class="calc-field" id="wt-from">
          <option value="lb">Pounds</option>
          <option value="kg">Kilograms</option>
        </select>
      </div>
      <button class="calc-btn" type="submit">Convert</button>
    </form>
    <div id="wt-conv-result" class="calc-result mt-4" aria-live="polite"></div>
  </div>

</div>

<script>
(function () {
  function round(n, d) { var f = Math.pow(10, d || 2); return Math.round(n * f) / f; }
  function bind(formId, outId, fn) {
    var form = document.getElementById(formId);
    var out  = document.getElementById(outId);
    if (!form) return;
    form.addEventListener('submit', function (e) {
      e.preventDefault();
      fn(out);
    });
  }

  bind('cap-conv', 'cap-conv-result', function (out) {
    var v = parseFloat(document.getElementById('cap-val').value);
    var from = document.getElementById('cap-from').value;
    if (isNaN(v)) { out.innerHTML = '<span class="calc-status calc-status-warn">Enter a value.</span>'; return; }
    var tons;
    if (from === 'tons') tons = v; else if (from === 'btu') tons = v / 12000; else tons = v / 3.51685;
    out.innerHTML = '<span class="calc-result-value">' + round(tons) + ' tons</span> &nbsp;&middot;&nbsp; ' +
      Math.round(tons * 12000).toLocaleString() + ' BTU/h &nbsp;&middot;&nbsp; ' + round(tons * 3.51685) + ' kW';
  });

  bind('temp-conv', 'temp-conv-result', function (out) {
    var v = parseFloat(document.getElementById('temp-val').value);
    var from = document.getElementById('temp-from').value;
    if (isNaN(v)) { out.innerHTML = '<span class="calc-status calc-status-warn">Enter a value.</span>'; return; }
    if (from === 'f') out.innerHTML = '<span class="calc-result-value">' + round(((v - 32) * 5) / 9) + ' &deg;C</span>';
    else out.innerHTML = '<span class="calc-result-value">' + round((v * 9) / 5 + 32) + ' &deg;F</span>';
  });

  bind('eff-conv', 'eff-conv-result', function (out) {
    var v = parseFloat(document.getElementById('eff-val').value);
    var from = document.getElementById('eff-from').value;
    if (isNaN(v)) { out.innerHTML = '<span class="calc-status calc-status-warn">Enter a value.</span>'; return; }
    if (from === 'eer') out.innerHTML = '<span class="calc-result-value">COP ' + round(v / 3.412) + '</span>';
    else out.innerHTML = '<span class="calc-result-value">EER ' + round(v * 3.412) + '</span>';
  });

  bind('pres-conv', 'pres-conv-result', function (out) {
    var v = parseFloat(document.getElementById('pres-val').value);
    var from = document.getElementById('pres-from').value;
    if (isNaN(v)) { out.innerHTML = '<span class="calc-status calc-status-warn">Enter a value.</span>'; return; }
    if (from === 'psi') out.innerHTML = '<span class="calc-result-value">' + round(v * 6.89476) + ' kPa</span>';
    else out.innerHTML = '<span class="calc-result-value">' + round(v / 6.89476) + ' PSI</span>';
  });

  bind('wt-conv', 'wt-conv-result', function (out) {
    var v = parseFloat(document.getElementById('wt-val').value);
    var from = document.getElementById('wt-from').value;
    if (isNaN(v)) { out.innerHTML = '<span class="calc-status calc-status-warn">Enter a value.</span>'; return; }
    if (from === 'lb') out.innerHTML = '<span class="calc-result-value">' + round(v * 0.453592) + ' kg</span>';
    else out.innerHTML = '<span class="calc-result-value">' + round(v / 0.453592) + ' lb</span>';
  });
})();
</script>

## Common HVAC unit conversions

This converter bundles the everyday unit conversions an HVAC tech reaches for, using standard factors:

| Conversion | Factor |
|---|---|
| Capacity | 1 ton = **12,000 BTU/h** = **3.517 kW** |
| Temperature | °F = °C × 9/5 + 32 &nbsp;·&nbsp; °C = (°F − 32) × 5/9 |
| Efficiency | **COP = EER ÷ 3.412** (EER = COP × 3.412) |
| Pressure | 1 PSI = **6.895 kPa** |
| Weight | 1 lb = **0.4536 kg** |

### Notes on efficiency ratings

- **EER** is a single-point rating (95°F outdoor) and **COP** is a dimensionless ratio of output to input — they convert exactly with the 3.412 BTU/h-per-watt factor.
- **SEER** is a *seasonal* average and does **not** convert exactly to EER. Field rules of thumb exist, but always use the manufacturer's published performance data for real numbers.

> Need to size airflow or capacity instead of just converting units? Use the [CFM & Tonnage Calculator]({{ "/tools/cfm-tonnage-calculator/" | relative_url }}).

## How FieldPad helps in the field

Convert on the spot here, then keep the job moving in **FieldPad** — log equipment specs, refrigerant weights, and capacities against the client and the system, and turn them straight into estimates and invoices. One offline-ready app, no re-keying, full history on every piece of equipment.
