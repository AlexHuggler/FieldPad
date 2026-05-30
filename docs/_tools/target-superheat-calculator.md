---
title: "Target Superheat Calculator (Fixed Orifice)"
short_title: "Target Superheat Calculator"
description: "Free target superheat calculator for fixed-orifice (piston) HVAC systems. Enter indoor wet bulb and outdoor dry bulb to get the target superheat you should be charging to."
card_blurb: "Find the target superheat for a fixed-orifice system from indoor wet-bulb and outdoor dry-bulb temperatures."
tool_category: "Refrigeration"
tool_order: 3
keywords: ["target superheat calculator", "fixed orifice superheat", "piston system charging", "superheat charging chart", "target superheat formula"]
formula_summary: "Target Superheat ≈ (3 × Indoor Wet Bulb − 80 − Outdoor Dry Bulb) ÷ 2 (common field approximation for fixed-orifice systems)."
how_to_name: "How to find target superheat on a fixed-orifice system"
how_to_steps:
  - { name: "Measure indoor wet bulb", text: "Take the return-air wet-bulb temperature at the indoor coil with a sling psychrometer or a digital meter." }
  - { name: "Measure outdoor dry bulb", text: "Take the outdoor ambient dry-bulb temperature in the shade near the condenser." }
  - { name: "Calculate target superheat", text: "Apply the field formula: Target SH = (3 × indoor wet bulb − 80 − outdoor dry bulb) ÷ 2." }
  - { name: "Compare and charge", text: "Compare your measured superheat to this target. Below target = overcharged (recover); above target = undercharged (add)." }
faqs:
  - { q: "What is target superheat?", a: "Target superheat is the superheat a fixed-orifice (piston) system should run at for the current indoor and outdoor conditions. Because a fixed orifice does not regulate superheat the way a TXV does, the correct value changes with load — so you calculate it from indoor wet bulb and outdoor dry bulb rather than using a single number." }
  - { q: "What is the target superheat formula?", a: "A widely used field approximation is: Target Superheat = (3 × indoor wet bulb − 80 − outdoor dry bulb) ÷ 2. Many manufacturers also publish a target superheat charging chart that you should use when available." }
  - { q: "Do TXV systems use target superheat?", a: "No. A TXV (or EEV) actively maintains superheat near 8–12°F, so you charge a TXV system to subcooling instead. Target superheat charging applies to fixed-orifice / piston metering devices." }
  - { q: "My measured superheat is below target — what does that mean?", a: "On a fixed-orifice system, measured superheat below target generally indicates an overcharge. Recover refrigerant slowly and recheck. Measured superheat above target generally indicates an undercharge." }
---

<div class="not-prose bg-white border border-gray-200 rounded-2xl p-6 my-8 shadow-sm">
  <form id="ts-form" class="grid grid-cols-1 sm:grid-cols-2 gap-5">
    <div>
      <label class="calc-label" for="ts-iwb">Indoor wet bulb (°F)</label>
      <input class="calc-field" type="number" inputmode="decimal" step="any" id="ts-iwb" placeholder="e.g. 63">
    </div>
    <div>
      <label class="calc-label" for="ts-odb">Outdoor dry bulb (°F)</label>
      <input class="calc-field" type="number" inputmode="decimal" step="any" id="ts-odb" placeholder="e.g. 95">
    </div>
    <div class="sm:col-span-2">
      <label class="calc-label" for="ts-actual">Measured superheat (°F, optional)</label>
      <input class="calc-field" type="number" inputmode="decimal" step="any" id="ts-actual" placeholder="compare to target">
    </div>
    <div class="sm:col-span-2 flex flex-wrap gap-3 items-center">
      <button class="calc-btn" type="submit">Calculate target superheat</button>
      <button class="text-sm text-gray-500 hover:text-fieldpad-700" type="reset">Reset</button>
    </div>
  </form>
  <div id="ts-result" class="calc-result mt-5" aria-live="polite"></div>
</div>

<script>
(function () {
  var form = document.getElementById('ts-form');
  var out  = document.getElementById('ts-result');
  if (!form) return;
  form.addEventListener('submit', function (e) {
    e.preventDefault();
    var iwb = parseFloat(document.getElementById('ts-iwb').value);
    var odb = parseFloat(document.getElementById('ts-odb').value);
    var act = parseFloat(document.getElementById('ts-actual').value);
    if (isNaN(iwb) || isNaN(odb)) {
      out.innerHTML = '<span class="calc-status calc-status-warn">Enter the indoor wet-bulb and outdoor dry-bulb temperatures.</span>';
      return;
    }
    var target = (3 * iwb - 80 - odb) / 2;
    var html;
    if (target < 0) {
      html = 'Target superheat: <span class="calc-result-value">~0 &deg;F</span>';
      html += '<span class="calc-status calc-status-warn">These conditions return a target at or below 0&deg;F. The system is at low load — verify your wet-bulb and dry-bulb readings, and use the manufacturer charging chart for low-load conditions.</span>';
    } else {
      html = 'Target superheat: <span class="calc-result-value">' + target.toFixed(1) + ' &deg;F</span>';
      if (!isNaN(act)) {
        var diff = act - target;
        if (Math.abs(diff) <= 3) {
          html += '<span class="calc-status calc-status-ok">Measured superheat is within ~3&deg;F of target — charge looks correct for these conditions.</span>';
        } else if (diff > 0) {
          html += '<span class="calc-status calc-status-alert">Measured superheat is ' + diff.toFixed(1) + '&deg;F above target — likely undercharged. Add refrigerant slowly and recheck.</span>';
        } else {
          html += '<span class="calc-status calc-status-alert">Measured superheat is ' + Math.abs(diff).toFixed(1) + '&deg;F below target — likely overcharged. Recover refrigerant slowly and recheck.</span>';
        }
      } else {
        html += '<span class="calc-status calc-status-ok">Charge the system until measured superheat matches this target. Re-measure conditions as you go.</span>';
      }
    }
    out.innerHTML = html;
  });
  form.addEventListener('reset', function () { out.innerHTML = ''; });
})();
</script>

## How target superheat works

A **fixed-orifice (piston) metering device** does not regulate superheat the way a thermostatic expansion valve does. As a result, the correct superheat for the system changes with the indoor load and the outdoor temperature. Instead of charging to a single number, you charge to a **target superheat** that you calculate from the current conditions.

A widely used field approximation is:

**Target Superheat (°F) = (3 × Indoor Wet Bulb − 80 − Outdoor Dry Bulb) ÷ 2**

You then compare your **measured** superheat (from the [Superheat Calculator]({{ "/tools/superheat-calculator/" | relative_url }})) to this target:

- Measured superheat **above** target → system is **undercharged** → add refrigerant.
- Measured superheat **below** target → system is **overcharged** → recover refrigerant.

### Worked example

Indoor wet bulb is **63°F** and outdoor dry bulb is **95°F**:

**(3 × 63 − 80 − 95) ÷ 2 = (189 − 175) ÷ 2 = 14 ÷ 2 = 7°F target superheat**

If your measured superheat is 12°F against a 7°F target, the system is running about 5°F high — undercharged — so you'd add refrigerant and recheck.

## Important cautions

- This formula is a **field approximation**. When the manufacturer publishes a target superheat **charging chart**, use it instead.
- The method is only valid for **fixed-orifice / piston** systems. Charge **TXV/EEV** systems to subcooling — see the [Subcooling Calculator]({{ "/tools/subcooling-calculator/" | relative_url }}).
- Accurate wet-bulb and dry-bulb readings matter. At very low indoor loads or low outdoor temperatures the calculated target can fall near zero, which is a sign to stop and use the manufacturer chart.

## How FieldPad helps in the field

Calculate the target here, then let **FieldPad** keep the whole charging record — measured conditions, target, final superheat — attached to the equipment and the client, ready to drop into a signed invoice. The next tech who pulls up this system sees exactly how it was charged last time.
