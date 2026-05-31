---
title: "Duct Size & Air Velocity Calculator"
short_title: "Duct Size & Velocity Calculator"
description: "Free HVAC duct calculator. Find air velocity (FPM) from CFM and duct size for round or rectangular ducts, plus equivalent round diameter, with ACCA Manual D target velocity ranges."
card_blurb: "Find air velocity from CFM and duct size (round or rectangular), plus equivalent diameter, against Manual D target ranges."
tool_category: "Airflow"
tool_order: 4
keywords: ["duct size calculator", "air velocity calculator", "fpm calculator hvac", "duct velocity", "equivalent duct diameter", "duct sizing"]
formula_summary: "Velocity (FPM) = CFM ÷ duct area (ft²). Round area = π(d/12/2)². Rectangular equivalent diameter De = 1.30 × (a·b)^0.625 ÷ (a+b)^0.25 (inches)."
how_to_name: "How to calculate duct air velocity"
how_to_steps:
  - { name: "Enter the airflow", text: "Enter the airflow through the duct in CFM." }
  - { name: "Enter the duct size", text: "For round duct, enter the diameter. For rectangular duct, enter width and height in inches." }
  - { name: "Get the velocity", text: "Velocity (FPM) = CFM ÷ duct cross-sectional area (ft²). The tool also gives the equivalent round diameter for rectangular duct." }
  - { name: "Compare to targets", text: "Check the velocity against Manual D ranges: ~600–900 FPM supply trunks, 400–600 FPM branches, 400–700 FPM returns." }
faqs:
  - { q: "How do you calculate duct air velocity?", a: "Velocity in feet per minute (FPM) = airflow (CFM) ÷ duct cross-sectional area (square feet). For a round duct, area = π × (diameter/2)² with the diameter converted to feet. For rectangular duct, area = width × height in feet." }
  - { q: "What is a good air velocity for ductwork?", a: "Typical ACCA Manual D targets are about 600–900 FPM in supply trunks, 400–600 FPM in supply branches, and 400–700 FPM in returns. Too high causes noise and high static pressure; too low allows poor distribution and dust settling." }
  - { q: "What is equivalent duct diameter?", a: "Equivalent diameter converts a rectangular duct to the round duct that has the same friction loss, so you can use round-duct friction charts. The Huebscher equation is De = 1.30 × (a × b)^0.625 ÷ (a + b)^0.25, where a and b are the rectangular dimensions in inches." }
  - { q: "Can this calculator size my ducts?", a: "It gives you velocity and equivalent diameter, which are exact geometry. Full duct sizing also requires a friction rate and fitting losses from an ACCA Manual D friction chart or a ductulator — those depend on duct material and layout and aren't captured by velocity alone. Use this for a quick velocity check, then size with Manual D." }
---

<div class="not-prose bg-white border border-gray-200 rounded-2xl p-6 my-8 shadow-sm">
  <form id="dz-form" class="space-y-5">
    <div class="grid grid-cols-1 sm:grid-cols-2 gap-5">
      <div>
        <label class="calc-label" for="dz-cfm">Airflow (CFM)</label>
        <input class="calc-field" type="number" inputmode="decimal" step="any" id="dz-cfm" placeholder="e.g. 1200">
      </div>
      <div>
        <label class="calc-label" for="dz-shape">Duct shape</label>
        <select class="calc-field" id="dz-shape">
          <option value="round">Round</option>
          <option value="rect">Rectangular</option>
        </select>
      </div>
    </div>
    <div id="dz-round-wrap">
      <label class="calc-label" for="dz-dia">Diameter (in)</label>
      <input class="calc-field" type="number" inputmode="decimal" step="any" id="dz-dia" placeholder="e.g. 14">
    </div>
    <div id="dz-rect-wrap" class="grid grid-cols-2 gap-5" style="display:none;">
      <div>
        <label class="calc-label" for="dz-w">Width (in)</label>
        <input class="calc-field" type="number" inputmode="decimal" step="any" id="dz-w" placeholder="e.g. 20">
      </div>
      <div>
        <label class="calc-label" for="dz-h">Height (in)</label>
        <input class="calc-field" type="number" inputmode="decimal" step="any" id="dz-h" placeholder="e.g. 8">
      </div>
    </div>
    <div class="flex flex-wrap gap-3 items-center">
      <button class="calc-btn" type="submit">Calculate velocity</button>
      <button class="text-sm text-gray-500 hover:text-fieldpad-700" type="reset">Reset</button>
    </div>
  </form>
  <div id="dz-result" class="calc-result mt-5" aria-live="polite"></div>
</div>

<script>
(function () {
  var form = document.getElementById('dz-form');
  var out  = document.getElementById('dz-result');
  var shape = document.getElementById('dz-shape');
  var roundWrap = document.getElementById('dz-round-wrap');
  var rectWrap = document.getElementById('dz-rect-wrap');
  if (!form) return;
  function syncShape() {
    if (shape.value === 'rect') { roundWrap.style.display = 'none'; rectWrap.style.display = 'grid'; }
    else { roundWrap.style.display = 'block'; rectWrap.style.display = 'none'; }
  }
  shape.addEventListener('change', syncShape);
  function verdict(fpm) {
    if (fpm < 400) return '<span class="calc-status calc-status-warn">Low velocity (under ~400 FPM) — risk of poor distribution and dust settling.</span>';
    if (fpm <= 700) return '<span class="calc-status calc-status-ok">Within typical branch/return range (about 400–700 FPM).</span>';
    if (fpm <= 900) return '<span class="calc-status calc-status-ok">Acceptable for a supply trunk (about 600–900 FPM). Keep returns and branches lower.</span>';
    return '<span class="calc-status calc-status-alert">High velocity (over ~900 FPM) — expect noise and high static pressure. Consider a larger duct.</span>';
  }
  form.addEventListener('submit', function (e) {
    e.preventDefault();
    var cfm = parseFloat(document.getElementById('dz-cfm').value);
    if (isNaN(cfm)) { out.innerHTML = '<span class="calc-status calc-status-warn">Enter the airflow in CFM.</span>'; return; }
    var areaFt, extra = '';
    if (shape.value === 'rect') {
      var w = parseFloat(document.getElementById('dz-w').value);
      var h = parseFloat(document.getElementById('dz-h').value);
      if (isNaN(w) || isNaN(h) || w <= 0 || h <= 0) { out.innerHTML = '<span class="calc-status calc-status-warn">Enter the duct width and height.</span>'; return; }
      areaFt = (w / 12) * (h / 12);
      var de = 1.30 * Math.pow(w * h, 0.625) / Math.pow(w + h, 0.25);
      extra = '<div class="mt-1 text-gray-600">Equivalent round diameter: <strong>' + de.toFixed(1) + ' in</strong></div>';
    } else {
      var d = parseFloat(document.getElementById('dz-dia').value);
      if (isNaN(d) || d <= 0) { out.innerHTML = '<span class="calc-status calc-status-warn">Enter the duct diameter.</span>'; return; }
      var rFt = (d / 12) / 2;
      areaFt = Math.PI * rFt * rFt;
    }
    var fpm = cfm / areaFt;
    out.innerHTML = 'Air velocity: <span class="calc-result-value">' + Math.round(fpm).toLocaleString() + ' FPM</span>' +
      '<div class="mt-1 text-gray-600">Duct area: ' + areaFt.toFixed(3) + ' ft²</div>' + extra + verdict(fpm);
  });
  form.addEventListener('reset', function () { out.innerHTML = ''; setTimeout(syncShape, 0); });
  syncShape();
})();
</script>

## How the duct velocity calculation works

Air velocity through a duct is simply the airflow divided by the duct's cross-sectional area:

**Velocity (FPM) = CFM ÷ Duct Area (ft²)**

- **Round duct area** = π × (diameter ÷ 2)², with the diameter converted from inches to feet.
- **Rectangular duct area** = (width ÷ 12) × (height ÷ 12) ft².

For rectangular duct, the tool also computes the **equivalent round diameter** using the Huebscher equation, so you can carry the result into a round-duct friction chart:

**De = 1.30 × (a × b)^0.625 ÷ (a + b)^0.25** (a, b in inches)

### Worked example

**1,200 CFM** through a **14"** round duct: area = π × (14/12/2)² = π × (0.583)² ≈ **1.069 ft²**, so velocity = 1,200 ÷ 1.069 ≈ **1,122 FPM** — high for a trunk; a 16" duct would bring it down.

## Target velocities (ACCA Manual D)

| Location | Typical FPM |
|---|---|
| Supply trunk | 600–900 |
| Supply branch | 400–600 |
| Return | 400–700 |

High velocity means **noise** and **high static pressure**; low velocity means **poor distribution**. Velocity is exact geometry — but full sizing also needs a **friction rate** and fitting losses from a Manual D friction chart or a ductulator, which depend on duct material and layout. Use this as a quick check, then size with Manual D, and confirm the system with the [Static Pressure Calculator]({{ "/tools/static-pressure-calculator/" | relative_url }}).

## How FieldPad helps in the field

Check duct velocity here, then record your airflow findings in **FieldPad** against the equipment and the job. Turn an undersized-duct diagnosis into a client-ready estimate, and keep the measurements on the same record as the invoice — offline, on your iPhone.
