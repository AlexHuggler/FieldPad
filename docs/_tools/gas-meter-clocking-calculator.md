---
title: "Gas Meter Clocking Calculator"
short_title: "Gas Meter Clocking Calculator"
description: "Free gas meter clocking calculator. Time one dial revolution to find a furnace or appliance's actual BTU/h input and cubic feet per hour, with adjustable gas heating value."
card_blurb: "Time one gas meter dial revolution to find actual BTU/h input and cubic feet per hour."
tool_category: "Heating & Gas"
tool_order: 2
keywords: ["gas meter clocking calculator", "clock a gas meter", "furnace btu input", "cfh gas calculator", "gas firing rate"]
formula_summary: "CFH = (3600 ÷ seconds per revolution) × test dial size (ft³). BTU/h input = CFH × gas heating value (BTU/ft³)."
how_to_name: "How to clock a gas meter"
how_to_steps:
  - { name: "Isolate the appliance", text: "Turn off every other gas appliance (water heater, range, etc.) so only the unit you're testing is drawing gas. Let it run at steady state." }
  - { name: "Time one revolution", text: "Pick a test dial (usually 1 or 2 ft³), watch the hand, and time exactly one full revolution with a stopwatch." }
  - { name: "Find cubic feet per hour", text: "CFH = (3600 ÷ seconds per revolution) × test dial size." }
  - { name: "Convert to BTU/h", text: "BTU/h input = CFH × gas heating value (default ~1,050 BTU/ft³ for natural gas; ask the utility for the exact value)." }
faqs:
  - { q: "How do you clock a gas meter?", a: "Shut off all other gas appliances, run the appliance you're testing, and time one full revolution of a known test dial (1 or 2 ft³) with a stopwatch. Cubic feet per hour = (3600 ÷ seconds per revolution) × dial size. Multiply CFH by the gas heating value to get BTU/h input." }
  - { q: "What heating value should I use for natural gas?", a: "Natural gas is commonly around 1,000–1,050 BTU per cubic foot, with 1,050 a frequent default. The exact value varies by region and gas composition — your local utility can give you the precise heating value for the most accurate result." }
  - { q: "Does clocking the meter measure efficiency?", a: "No. Clocking measures the actual fuel input (firing rate). Efficiency would be the BTU output divided by this input. To get output you'd measure the temperature rise and airflow. Compare the clocked input to the furnace nameplate input to spot an over- or under-fired unit." }
  - { q: "How long should the test take?", a: "Aim for a revolution time of about 30–120 seconds. Very short times (under ~15 seconds) amplify stopwatch error; very long ones get tedious. A 2 ft³ dial gives a longer, more accurate timing on high-input appliances." }
---

<div class="not-prose bg-white border border-gray-200 rounded-2xl p-6 my-8 shadow-sm">
  <form id="gm-form" class="grid grid-cols-1 sm:grid-cols-3 gap-5">
    <div>
      <label class="calc-label" for="gm-sec">Seconds per revolution</label>
      <input class="calc-field" type="number" inputmode="decimal" step="any" id="gm-sec" placeholder="e.g. 42">
    </div>
    <div>
      <label class="calc-label" for="gm-dial">Test dial size (ft³)</label>
      <select class="calc-field" id="gm-dial">
        <option value="1">1 ft³</option>
        <option value="2">2 ft³</option>
        <option value="0.5">1/2 ft³</option>
        <option value="5">5 ft³</option>
      </select>
    </div>
    <div>
      <label class="calc-label" for="gm-hv">Heating value (BTU/ft³)</label>
      <input class="calc-field" type="number" inputmode="decimal" step="any" id="gm-hv" value="1050">
    </div>
    <div class="sm:col-span-3 flex flex-wrap gap-3 items-center">
      <button class="calc-btn" type="submit">Calculate input</button>
      <button class="text-sm text-gray-500 hover:text-fieldpad-700" type="reset">Reset</button>
    </div>
  </form>
  <div id="gm-result" class="calc-result mt-5" aria-live="polite"></div>
</div>

<script>
(function () {
  var form = document.getElementById('gm-form');
  var out  = document.getElementById('gm-result');
  if (!form) return;
  form.addEventListener('submit', function (e) {
    e.preventDefault();
    var sec  = parseFloat(document.getElementById('gm-sec').value);
    var dial = parseFloat(document.getElementById('gm-dial').value);
    var hv   = parseFloat(document.getElementById('gm-hv').value);
    if (isNaN(hv)) hv = 1050;
    if (isNaN(sec) || sec <= 0) {
      out.innerHTML = '<span class="calc-status calc-status-warn">Enter the seconds for one full dial revolution.</span>';
      return;
    }
    var cfh = (3600 / sec) * dial;
    var btu = cfh * hv;
    var html = 'Gas input: <span class="calc-result-value">' + Math.round(btu).toLocaleString() + ' BTU/h</span>' +
      '<div class="mt-1 text-gray-600">' + Math.round(cfh).toLocaleString() + ' cubic feet per hour</div>';
    if (sec < 15) html += '<span class="calc-status calc-status-warn">Short revolution time — stopwatch error is amplified. Use a larger test dial or re-time for accuracy.</span>';
    else html += '<span class="calc-status calc-status-ok">Measures input (firing rate), not efficiency. Compare to the furnace nameplate input, and use your utility\'s exact heating value for best accuracy.</span>';
    out.innerHTML = html;
  });
  form.addEventListener('reset', function () { out.innerHTML = ''; });
})();
</script>

## How gas meter clocking works

Clocking the meter measures an appliance's **actual gas input** by timing how fast it burns a known volume of gas:

**CFH = (3600 ÷ seconds per revolution) × test dial size (ft³)**

**BTU/h input = CFH × gas heating value (BTU/ft³)**

The `3600` converts seconds to an hour. Natural gas runs about **1,000–1,050 BTU/ft³** (1,050 is a common default) — your utility can give the exact value, which is editable above.

### Worked example

A furnace turns a **1 ft³** test dial in **42 seconds** on natural gas at **1,050 BTU/ft³**:

- CFH = (3600 ÷ 42) × 1 = **85.7 ft³/h**
- BTU/h = 85.7 × 1,050 ≈ **90,000 BTU/h** input

Compare that to the nameplate input to confirm the furnace is firing correctly.

## Procedure notes

- **Isolate the appliance** — shut off all other gas loads so only the test unit is drawing gas.
- **Time 30–120 seconds** for accuracy; use a **2 ft³** dial on high-input appliances for a longer, steadier read.
- This measures **input only**, not efficiency. To get output, pair it with the [Furnace Temperature Rise Calculator]({{ "/tools/furnace-temp-rise-calculator/" | relative_url }}) (output = input × efficiency).

> **Safety first.** Work on gas-fired equipment only within your training and local codes. Always check for leaks and proper combustion after any gas work.

## How FieldPad helps in the field

Clock the meter here, then record the firing rate in **FieldPad** against the equipment and the job. Note an over- or under-fired furnace, attach it to the service record, and turn the correction into a documented estimate — offline, on your iPhone.
