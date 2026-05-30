---
title: "CFM & Tonnage Calculator"
short_title: "CFM & Tonnage Calculator"
description: "Free HVAC airflow calculator. Find required CFM from sensible BTU/h and Delta T, convert tons to CFM with the 400 CFM/ton rule, and convert between tons, BTU/h, and kW."
card_blurb: "Find required CFM from sensible heat and Delta T, apply the 400 CFM/ton rule, and convert tons, BTU/h, and kW."
tool_category: "Airflow"
tool_order: 2
keywords: ["cfm calculator", "hvac tonnage calculator", "400 cfm per ton", "btu to tons", "airflow calculator", "cfm formula hvac"]
formula_summary: "CFM = Sensible Heat (BTU/h) ÷ (1.08 × Delta T). Nominal airflow ≈ 400 CFM per ton. Tons = BTU/h ÷ 12,000. 1 ton = 12,000 BTU/h = 3.517 kW."
how_to_name: "How to calculate required airflow (CFM)"
how_to_steps:
  - { name: "Find the sensible heat", text: "Use the system's sensible cooling capacity in BTU/h (from the equipment data), or estimate it." }
  - { name: "Measure the temperature split", text: "Measure the dry-bulb Delta T across the evaporator coil (return minus supply)." }
  - { name: "Apply the formula", text: "CFM = sensible BTU/h ÷ (1.08 × Delta T)." }
  - { name: "Sanity check against tonnage", text: "Compare to the 400 CFM/ton rule of thumb (350–450 CFM/ton range) for the system's nominal tonnage." }
faqs:
  - { q: "What is the 400 CFM per ton rule?", a: "It's a rule of thumb that a typical air conditioning system needs roughly 400 CFM of airflow per ton of cooling (400 CFM × tons). The practical range is about 350–450 CFM/ton — closer to 350 in humid climates where more dehumidification is wanted, and up to 450 in dry climates." }
  - { q: "How do you calculate CFM from BTU and Delta T?", a: "CFM = sensible heat in BTU/h ÷ (1.08 × Delta T). The 1.08 factor comes from the specific heat and density of standard air (1.08 = 60 min/h × 0.075 lb/ft³ × 0.24 BTU/lb·°F)." }
  - { q: "How many BTU is a ton of cooling?", a: "One ton of refrigeration equals 12,000 BTU/h, which is about 3.517 kW. So a 3-ton system is 36,000 BTU/h." }
  - { q: "Why is the airflow factor 1.08?", a: "1.08 is the sensible heat factor for standard air at sea level. It is the product of 60 minutes per hour, the air density of 0.075 lb/ft³, and the specific heat of air at 0.24 BTU per pound per °F (60 × 0.075 × 0.24 = 1.08)." }
---

<div class="not-prose bg-white border border-gray-200 rounded-2xl p-6 my-8 shadow-sm">
  <h3 class="text-base font-bold text-fieldpad-900 mb-4">Required airflow from sensible heat</h3>
  <form id="cfm-form" class="grid grid-cols-1 sm:grid-cols-2 gap-5">
    <div>
      <label class="calc-label" for="cfm-btu">Sensible heat (BTU/h)</label>
      <input class="calc-field" type="number" inputmode="decimal" step="any" id="cfm-btu" placeholder="e.g. 24000">
    </div>
    <div>
      <label class="calc-label" for="cfm-dt">Delta T across coil (°F)</label>
      <input class="calc-field" type="number" inputmode="decimal" step="any" id="cfm-dt" placeholder="e.g. 20">
    </div>
    <div class="sm:col-span-2 flex flex-wrap gap-3 items-center">
      <button class="calc-btn" type="submit">Calculate CFM</button>
      <button class="text-sm text-gray-500 hover:text-fieldpad-700" type="reset">Reset</button>
    </div>
  </form>
  <div id="cfm-result" class="calc-result mt-5" aria-live="polite"></div>

  <hr class="my-6 border-gray-200">

  <h3 class="text-base font-bold text-fieldpad-900 mb-4">Tonnage &harr; BTU/h &harr; kW &amp; airflow</h3>
  <form id="ton-form" class="grid grid-cols-1 sm:grid-cols-3 gap-5">
    <div>
      <label class="calc-label" for="ton-value">Value</label>
      <input class="calc-field" type="number" inputmode="decimal" step="any" id="ton-value" placeholder="e.g. 3">
    </div>
    <div>
      <label class="calc-label" for="ton-unit">Unit</label>
      <select class="calc-field" id="ton-unit">
        <option value="tons">Tons</option>
        <option value="btu">BTU/h</option>
        <option value="kw">kW</option>
      </select>
    </div>
    <div>
      <label class="calc-label" for="ton-cfmpt">CFM per ton</label>
      <select class="calc-field" id="ton-cfmpt">
        <option value="400">400 (standard)</option>
        <option value="350">350 (humid)</option>
        <option value="450">450 (dry)</option>
      </select>
    </div>
    <div class="sm:col-span-3 flex flex-wrap gap-3 items-center">
      <button class="calc-btn" type="submit">Convert</button>
      <button class="text-sm text-gray-500 hover:text-fieldpad-700" type="reset">Reset</button>
    </div>
  </form>
  <div id="ton-result" class="calc-result mt-5" aria-live="polite"></div>
</div>

<script>
(function () {
  var cfmForm = document.getElementById('cfm-form');
  var cfmOut  = document.getElementById('cfm-result');
  if (cfmForm) {
    cfmForm.addEventListener('submit', function (e) {
      e.preventDefault();
      var btu = parseFloat(document.getElementById('cfm-btu').value);
      var dt  = parseFloat(document.getElementById('cfm-dt').value);
      if (isNaN(btu) || isNaN(dt) || dt === 0) {
        cfmOut.innerHTML = '<span class="calc-status calc-status-warn">Enter the sensible heat and a non-zero Delta T.</span>';
        return;
      }
      var cfm = btu / (1.08 * dt);
      cfmOut.innerHTML = 'Required airflow: <span class="calc-result-value">' + Math.round(cfm).toLocaleString() + ' CFM</span>';
    });
    cfmForm.addEventListener('reset', function () { cfmOut.innerHTML = ''; });
  }

  var tonForm = document.getElementById('ton-form');
  var tonOut  = document.getElementById('ton-result');
  if (tonForm) {
    tonForm.addEventListener('submit', function (e) {
      e.preventDefault();
      var v    = parseFloat(document.getElementById('ton-value').value);
      var unit = document.getElementById('ton-unit').value;
      var cpt  = parseFloat(document.getElementById('ton-cfmpt').value);
      if (isNaN(v)) {
        tonOut.innerHTML = '<span class="calc-status calc-status-warn">Enter a value to convert.</span>';
        return;
      }
      var tons;
      if (unit === 'tons') { tons = v; }
      else if (unit === 'btu') { tons = v / 12000; }
      else { tons = v / 3.51685; }
      var btu = tons * 12000;
      var kw  = tons * 3.51685;
      var cfm = tons * cpt;
      tonOut.innerHTML =
        '<div class="calc-result-value">' + (Math.round(tons * 100) / 100) + ' tons</div>' +
        '<div class="mt-1">' + Math.round(btu).toLocaleString() + ' BTU/h &nbsp;&middot;&nbsp; ' +
        (Math.round(kw * 100) / 100) + ' kW</div>' +
        '<span class="calc-status calc-status-ok">Nominal airflow at ' + cpt + ' CFM/ton: <strong>' + Math.round(cfm).toLocaleString() + ' CFM</strong></span>';
    });
    tonForm.addEventListener('reset', function () { tonOut.innerHTML = ''; });
  }
})();
</script>

## How the airflow calculations work

### Required CFM from sensible heat

The amount of air a system must move to deliver its sensible capacity at a given temperature split is:

**CFM = Sensible Heat (BTU/h) ÷ (1.08 × Delta T)**

The **1.08** is the sensible heat factor for standard air — it's `60 min/h × 0.075 lb/ft³ × 0.24 BTU/lb·°F`. Rearranged, the same relationship gives you sensible heat (`Qs = 1.08 × CFM × ΔT`) if you already know airflow.

**Worked example:** A system with **24,000 BTU/h** sensible capacity across a **20°F** split needs:

**24,000 ÷ (1.08 × 20) = 24,000 ÷ 21.6 = 1,111 CFM**

### Tonnage, BTU/h, kW, and the 400 CFM/ton rule

- **1 ton = 12,000 BTU/h = 3.517 kW**
- **Tons = BTU/h ÷ 12,000**
- Nominal airflow ≈ **400 CFM per ton** (practical range **350–450**: lean toward 350 in humid climates for more dehumidification, up to 450 in dry climates).

A 3-ton system is therefore 36,000 BTU/h and wants roughly **1,200 CFM** at the 400 CFM/ton rule.

## How FieldPad helps in the field

Size the airflow here, then keep the numbers where the work lives. **FieldPad** logs measured airflow and capacity against the equipment, ties them to the job, and turns your findings into a client-ready estimate or invoice — offline, on your iPhone, no re-keying.
