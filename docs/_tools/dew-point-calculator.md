---
title: "Dew Point & Relative Humidity Calculator"
short_title: "Dew Point & RH Calculator"
description: "Free dew point calculator for HVAC. Find the dew point from dry-bulb temperature and relative humidity (or solve RH from dry-bulb and dew point), with condensation-risk guidance."
card_blurb: "Find dew point from temperature and RH (or RH from temperature and dew point), with condensation-risk flags."
tool_category: "Psychrometrics"
tool_order: 2
keywords: ["dew point calculator", "relative humidity calculator", "condensation risk", "magnus formula dew point", "hvac humidity calculator"]
formula_summary: "Magnus formula: α = ln(RH/100) + (a·T)/(b+T); dew point Td = (b·α)/(a−α), with a = 17.27, b = 237.7 (°C). Reverse solves RH from T and Td."
how_to_name: "How to calculate dew point"
how_to_steps:
  - { name: "Pick what you know", text: "Choose to find dew point from temperature and relative humidity, or RH from temperature and dew point." }
  - { name: "Enter the values", text: "Enter the dry-bulb temperature and the second value (RH% or dew point), in °F." }
  - { name: "Read the result", text: "The Magnus formula returns the dew point (or RH). If a surface is at or below the dew point, condensation will form." }
faqs:
  - { q: "What is dew point?", a: "Dew point is the temperature at which air becomes saturated and moisture begins to condense. When any surface cools to or below the dew point, water condenses on it. It's a direct measure of how much moisture is in the air, independent of the air temperature." }
  - { q: "How do you calculate dew point from temperature and humidity?", a: "Use the Magnus formula: compute α = ln(RH/100) + (a·T)/(b+T), then dew point Td = (b·α)/(a−α), with constants a = 17.27 and b = 237.7 when temperature is in °C. Convert °F to °C first, then convert the result back to °F. It's accurate to about ±0.4°C from 0–60°C." }
  - { q: "When does condensation form?", a: "Condensation forms when a surface temperature drops to or below the air's dew point. For example, if the indoor dew point is 55°F, any duct, window, or pipe surface at 55°F or colder will sweat. Keeping surfaces above the dew point — or lowering the dew point with dehumidification — prevents it." }
  - { q: "What relative humidity promotes mold?", a: "Sustained indoor relative humidity above about 60% encourages mold growth. Keeping indoor RH in the 40–60% range is a common comfort and health target. Dew point matters too: high dew points mean cold surfaces will sweat and feed mold." }
---

<div class="not-prose bg-white border border-gray-200 rounded-2xl p-6 my-8 shadow-sm">
  <form id="dp-form" class="grid grid-cols-1 sm:grid-cols-3 gap-5">
    <div>
      <label class="calc-label" for="dp-mode">Find</label>
      <select class="calc-field" id="dp-mode">
        <option value="dp">Dew point (from T &amp; RH)</option>
        <option value="rh">RH (from T &amp; dew point)</option>
      </select>
    </div>
    <div>
      <label class="calc-label" for="dp-temp">Dry-bulb temp (°F)</label>
      <input class="calc-field" type="number" inputmode="decimal" step="any" id="dp-temp" placeholder="e.g. 75">
    </div>
    <div>
      <label class="calc-label" for="dp-second"><span id="dp-second-label">Relative humidity (%)</span></label>
      <input class="calc-field" type="number" inputmode="decimal" step="any" id="dp-second" placeholder="e.g. 50">
    </div>
    <div class="sm:col-span-3 flex flex-wrap gap-3 items-center">
      <button class="calc-btn" type="submit">Calculate</button>
      <button class="text-sm text-gray-500 hover:text-fieldpad-700" type="reset">Reset</button>
    </div>
  </form>
  <div id="dp-result" class="calc-result mt-5" aria-live="polite"></div>
</div>

<script>
(function () {
  var A = 17.27, B = 237.7;
  var form = document.getElementById('dp-form');
  var out  = document.getElementById('dp-result');
  var mode = document.getElementById('dp-mode');
  var label = document.getElementById('dp-second-label');
  var second = document.getElementById('dp-second');
  if (!form) return;
  var f2c = function (f) { return (f - 32) * 5 / 9; };
  var c2f = function (c) { return c * 9 / 5 + 32; };
  function syncMode() {
    if (mode.value === 'rh') { label.textContent = 'Dew point (°F)'; second.placeholder = 'e.g. 55'; }
    else { label.textContent = 'Relative humidity (%)'; second.placeholder = 'e.g. 50'; }
  }
  mode.addEventListener('change', syncMode);
  function riskNote(dpF, tF) {
    var note = 'Any surface at or below ' + dpF.toFixed(1) + '&deg;F will condense moisture.';
    if (tF - dpF < 5) return '<span class="calc-status calc-status-alert">Air is near saturation — condensation is likely on cool surfaces. ' + note + '</span>';
    return '<span class="calc-status calc-status-ok">' + note + '</span>';
  }
  form.addEventListener('submit', function (e) {
    e.preventDefault();
    var tF = parseFloat(document.getElementById('dp-temp').value);
    var v2 = parseFloat(second.value);
    if (isNaN(tF) || isNaN(v2)) { out.innerHTML = '<span class="calc-status calc-status-warn">Enter both values.</span>'; return; }
    var tC = f2c(tF);
    if (mode.value === 'dp') {
      if (v2 <= 0 || v2 > 100) { out.innerHTML = '<span class="calc-status calc-status-warn">Relative humidity must be between 0 and 100%.</span>'; return; }
      var alpha = Math.log(v2 / 100) + (A * tC) / (B + tC);
      var dpC = (B * alpha) / (A - alpha);
      var dpF = c2f(dpC);
      out.innerHTML = 'Dew point: <span class="calc-result-value">' + dpF.toFixed(1) + ' &deg;F</span>' + riskNote(dpF, tF);
    } else {
      var dpC = f2c(v2);
      if (dpC > tC) { out.innerHTML = '<span class="calc-status calc-status-warn">Dew point can\'t be higher than the dry-bulb temperature.</span>'; return; }
      var rh = 100 * Math.exp((A * dpC) / (B + dpC)) / Math.exp((A * tC) / (B + tC));
      out.innerHTML = 'Relative humidity: <span class="calc-result-value">' + rh.toFixed(1) + ' %</span>' + riskNote(v2, tF);
    }
  });
  form.addEventListener('reset', function () { out.innerHTML = ''; setTimeout(syncMode, 0); });
  syncMode();
})();
</script>

## How the dew point calculation works

**Dew point** is the temperature at which air becomes saturated and water starts to condense. It's a direct measure of the moisture in the air. This tool uses the well-validated **Magnus formula**:

**α = ln(RH ÷ 100) + (a × T) ÷ (b + T)**

**Dew Point = (b × α) ÷ (a − α)**

with constants **a = 17.27** and **b = 237.7** when temperature is in **°C**. Fahrenheit inputs are converted to Celsius, solved, and converted back. The formula is accurate to about **±0.4°C** across the 0–60°C range. The calculator also runs it in reverse to find **relative humidity** from dry-bulb temperature and a measured dew point.

### Worked example

At **75°F** and **50% RH**, the dew point is about **55°F** — so any surface at 55°F or colder (a cold supply duct, a chilled-water pipe, a window) will sweat.

## Why dew point matters

- **Condensation** forms whenever a surface cools to or below the dew point. That's the cause of sweating ducts, window fogging, and hidden moisture in wall cavities.
- **Mold risk** climbs when indoor RH stays above ~60%. A comfortable, healthy target is roughly **40–60% RH**.
- Controlling dew point (through dehumidification or by keeping surfaces warm) is how you stop condensation problems.

> Quantifying the moisture load that comes with that humidity? Use the [Sensible & Latent Heat Calculator]({{ "/tools/sensible-latent-heat-calculator/" | relative_url }}).

## How FieldPad helps in the field

Check the dew point here, then document humidity and condensation findings in **FieldPad** against the equipment and the client. Turn a humidity or sweating-duct diagnosis into a client-ready estimate, all on one offline record that travels with the job.
