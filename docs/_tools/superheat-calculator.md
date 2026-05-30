---
title: "Superheat Calculator"
short_title: "Superheat Calculator"
description: "Free superheat calculator for HVAC techs. Enter the suction line temperature and the saturation temperature to get measured superheat in °F, with target ranges for TXV and fixed-orifice systems."
card_blurb: "Calculate measured superheat from suction line temperature and saturation temperature, with TXV and fixed-orifice target guidance."
tool_category: "Refrigeration"
tool_order: 1
keywords: ["superheat calculator", "hvac superheat", "suction superheat", "txv superheat", "how to calculate superheat", "superheat formula"]
formula_summary: "Superheat = Suction Line Temperature − Saturation Temperature (at the measured suction pressure)."
how_to_name: "How to calculate superheat"
how_to_steps:
  - { name: "Measure suction line temperature", text: "Clamp an accurate thermometer on the suction line about 6 inches from the compressor and let it stabilize." }
  - { name: "Find the saturation temperature", text: "Read the suction (low-side) pressure on your gauges and convert it to saturation temperature using the refrigerant's P/T chart." }
  - { name: "Subtract", text: "Superheat = suction line temperature − saturation temperature. The result is in degrees Fahrenheit." }
  - { name: "Compare to target", text: "For a TXV system, target 8–12°F. For a fixed-orifice (piston) system, calculate target superheat from indoor wet bulb and outdoor dry bulb." }
faqs:
  - { q: "What is a good superheat reading?", a: "On a TXV or EEV system, target superheat is typically 8–12°F at the evaporator outlet. On a fixed-orifice (piston) system, the correct superheat depends on indoor wet-bulb and outdoor dry-bulb conditions and is found with a target superheat calculation rather than a single number." }
  - { q: "How do you calculate superheat?", a: "Superheat = suction line temperature − saturation temperature. Measure the suction line temperature with a clamp thermometer, read the low-side pressure, convert that pressure to a saturation temperature with the refrigerant's P/T chart, then subtract." }
  - { q: "What does low superheat mean?", a: "Low superheat (near 0°F) means liquid refrigerant may be leaving the evaporator and returning to the compressor, which risks flooding and compressor damage. Common causes include overcharge, a TXV feeding too much, or low airflow across the evaporator." }
  - { q: "What does high superheat mean?", a: "High superheat means the refrigerant boils off well before the end of the coil, so the system is starved. Common causes include undercharge, a restriction (clogged filter-drier or TXV), or a refrigerant leak." }
---

<div class="not-prose bg-white border border-gray-200 rounded-2xl p-6 my-8 shadow-sm">
  <form id="sh-form" class="grid grid-cols-1 sm:grid-cols-2 gap-5">
    <div>
      <label class="calc-label" for="sh-line">Suction line temperature (°F)</label>
      <input class="calc-field" type="number" inputmode="decimal" step="any" id="sh-line" placeholder="e.g. 52">
    </div>
    <div>
      <label class="calc-label" for="sh-sat">Saturation temperature (°F)</label>
      <input class="calc-field" type="number" inputmode="decimal" step="any" id="sh-sat" placeholder="e.g. 40">
    </div>
    <div class="sm:col-span-2">
      <label class="calc-label" for="sh-system">System metering device</label>
      <select class="calc-field" id="sh-system">
        <option value="txv">TXV / EEV (target 8–12°F)</option>
        <option value="fixed">Fixed orifice / piston</option>
      </select>
    </div>
    <div class="sm:col-span-2 flex flex-wrap gap-3 items-center">
      <button class="calc-btn" type="submit">Calculate superheat</button>
      <button class="text-sm text-gray-500 hover:text-fieldpad-700" type="reset">Reset</button>
    </div>
  </form>
  <div id="sh-result" class="calc-result mt-5" aria-live="polite"></div>
</div>

<script>
(function () {
  var form = document.getElementById('sh-form');
  var out  = document.getElementById('sh-result');
  if (!form) return;
  form.addEventListener('submit', function (e) {
    e.preventDefault();
    var line = parseFloat(document.getElementById('sh-line').value);
    var sat  = parseFloat(document.getElementById('sh-sat').value);
    var sys  = document.getElementById('sh-system').value;
    if (isNaN(line) || isNaN(sat)) {
      out.innerHTML = '<span class="calc-status calc-status-warn">Enter both the suction line temperature and the saturation temperature.</span>';
      return;
    }
    var sh = line - sat;
    var html = 'Measured superheat: <span class="calc-result-value">' + sh.toFixed(1) + ' &deg;F</span>';
    if (sys === 'txv') {
      if (sh < 4) {
        html += '<span class="calc-status calc-status-alert">Very low superheat — risk of liquid floodback to the compressor. Check for overcharge, TXV overfeeding, or low evaporator airflow.</span>';
      } else if (sh <= 14) {
        html += '<span class="calc-status calc-status-ok">In the typical TXV range (about 8–12&deg;F). Confirm against the equipment data.</span>';
      } else {
        html += '<span class="calc-status calc-status-warn">High superheat for a TXV system — possible undercharge, restriction, or a starving metering device.</span>';
      }
    } else {
      html += '<span class="calc-status calc-status-warn">Fixed-orifice systems have no single target. Compare this value to your <a class="underline" href="' + '{{ "/tools/target-superheat-calculator/" | relative_url }}' + '">target superheat</a> for the current indoor wet-bulb and outdoor dry-bulb conditions.</span>';
    }
    out.innerHTML = html;
  });
  form.addEventListener('reset', function () { out.innerHTML = ''; });
})();
</script>

## How the superheat calculation works

**Superheat** is the number of degrees the refrigerant vapor has risen *above* its saturation (boiling) temperature at the suction pressure. It tells you how much of the evaporator is being used to boil liquid into vapor versus how much is just heating already-boiled vapor.

The formula is simple:

**Superheat (°F) = Suction Line Temperature − Saturation Temperature**

The saturation temperature is not measured directly — you read the low-side (suction) pressure on your gauges and convert it to a saturation temperature using the pressure–temperature (P/T) chart for the specific refrigerant in the system. Most digital manifolds do this conversion automatically once you select the refrigerant.

### Worked example

A R-410A system reads a suction line temperature of **52°F** and the gauges show a suction saturation temperature of **40°F**:

**52 − 40 = 12°F of superheat**

On a TXV system, 12°F sits right at the top of the normal 8–12°F band — a healthy reading.

## What your superheat reading is telling you

- **TXV / EEV systems** hold superheat fairly constant (typically **8–12°F**) across a range of conditions, because the valve modulates to maintain it. If a TXV system is far outside that band, suspect the charge, airflow, or the valve itself.
- **Fixed-orifice (piston) systems** do not control superheat — it floats with indoor and outdoor conditions. There is no single "correct" number. You compare the measured superheat to a **target superheat** calculated from the indoor wet-bulb and outdoor dry-bulb temperatures.

| Reading | Likely meaning |
|---|---|
| Low superheat (under ~5°F) | Overcharge, TXV overfeeding, or low evaporator airflow — risk of liquid floodback |
| Normal (8–12°F on a TXV) | System is charged and metering correctly |
| High superheat (over ~14°F) | Undercharge, restriction, or a starving metering device |

> Superheat and subcooling are read together. On a TXV system you charge to **subcooling** and use superheat as a check; on a fixed-orifice system you charge to **superheat**.

## How FieldPad helps in the field

This calculator gives you the number on the spot. **FieldPad** keeps it with the work: log the reading against the equipment profile and the client, attach it to the service job, and turn the diagnosis into a signed estimate or invoice without re-keying anything. Your charging history lives on the same equipment record as the invoices, so the next visit starts where this one left off.
