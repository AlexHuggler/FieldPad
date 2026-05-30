---
title: "Total External Static Pressure (TESP) Calculator"
short_title: "Static Pressure Calculator"
description: "Free total external static pressure calculator for HVAC techs. Add supply and return static readings to get TESP in inches of water column, compared against the equipment's rated maximum."
card_blurb: "Add supply and return static pressure readings to get TESP and compare it to the equipment's rated maximum."
tool_category: "Airflow"
tool_order: 3
keywords: ["static pressure calculator", "TESP calculator", "total external static pressure", "hvac static pressure", "inches water column", "duct static pressure"]
formula_summary: "TESP = |Supply Static Pressure| + |Return Static Pressure| (in inches of water column)."
how_to_name: "How to measure total external static pressure"
how_to_steps:
  - { name: "Drill or use test ports", text: "Use test ports on the supply side after the air handler/furnace (downstream of the coil) and on the return side before the blower (upstream of the filter is common)." }
  - { name: "Read supply static", text: "Read the supply-side static pressure with a manometer; it will read positive." }
  - { name: "Read return static", text: "Read the return-side static pressure; it will read negative — use its absolute value." }
  - { name: "Add the absolute values", text: "TESP = |supply static| + |return static|. Compare to the rated maximum on the equipment (often 0.5 in. w.c.)." }
faqs:
  - { q: "What is a good total external static pressure?", a: "Most residential air handlers and furnaces are rated for a maximum total external static pressure of about 0.5 inches of water column (in. w.c.). Many real systems run higher than rated, which restricts airflow. Always check the rated maximum on the specific equipment's data tag." }
  - { q: "How do you calculate total external static pressure?", a: "TESP = the absolute value of the supply-side static pressure plus the absolute value of the return-side static pressure. The supply reads positive and the return reads negative, so you add their magnitudes together." }
  - { q: "What does high static pressure mean?", a: "High TESP means the duct system is too restrictive for the blower — undersized ducts, a dirty or high-MERV filter, a closed or kinked run, a dirty coil, or too few/small grilles. High static reduces airflow, hurts capacity, and shortens blower life." }
  - { q: "Where do I take static pressure readings?", a: "Take the supply reading downstream of the air handler/furnace and coil, and the return reading upstream of the blower. Avoid placing probes directly in turbulent spots right at the blower outlet. Reading across the filter separately also tells you how much the filter alone is contributing." }
---

<div class="not-prose bg-white border border-gray-200 rounded-2xl p-6 my-8 shadow-sm">
  <form id="sp-form" class="grid grid-cols-1 sm:grid-cols-2 gap-5">
    <div>
      <label class="calc-label" for="sp-supply">Supply static (in. w.c.)</label>
      <input class="calc-field" type="number" inputmode="decimal" step="any" id="sp-supply" placeholder="e.g. 0.35">
    </div>
    <div>
      <label class="calc-label" for="sp-return">Return static (in. w.c.)</label>
      <input class="calc-field" type="number" inputmode="decimal" step="any" id="sp-return" placeholder="e.g. -0.30">
    </div>
    <div class="sm:col-span-2">
      <label class="calc-label" for="sp-rated">Rated max TESP (in. w.c., optional)</label>
      <input class="calc-field" type="number" inputmode="decimal" step="any" id="sp-rated" placeholder="e.g. 0.5">
    </div>
    <div class="sm:col-span-2 flex flex-wrap gap-3 items-center">
      <button class="calc-btn" type="submit">Calculate TESP</button>
      <button class="text-sm text-gray-500 hover:text-fieldpad-700" type="reset">Reset</button>
    </div>
  </form>
  <div id="sp-result" class="calc-result mt-5" aria-live="polite"></div>
</div>

<script>
(function () {
  var form = document.getElementById('sp-form');
  var out  = document.getElementById('sp-result');
  if (!form) return;
  form.addEventListener('submit', function (e) {
    e.preventDefault();
    var sup = parseFloat(document.getElementById('sp-supply').value);
    var ret = parseFloat(document.getElementById('sp-return').value);
    var rated = parseFloat(document.getElementById('sp-rated').value);
    if (isNaN(sup) || isNaN(ret)) {
      out.innerHTML = '<span class="calc-status calc-status-warn">Enter both the supply and return static readings.</span>';
      return;
    }
    var tesp = Math.abs(sup) + Math.abs(ret);
    var limit = !isNaN(rated) ? rated : 0.5;
    var labelNote = !isNaN(rated) ? '' : ' (assuming a 0.5 in. w.c. rating — confirm on the data tag)';
    var html = 'Total external static pressure: <span class="calc-result-value">' + tesp.toFixed(2) + ' in. w.c.</span>';
    if (tesp <= limit) {
      html += '<span class="calc-status calc-status-ok">At or below the ' + limit.toFixed(2) + ' in. w.c. rated maximum' + labelNote + '.</span>';
    } else {
      html += '<span class="calc-status calc-status-alert">Above the ' + limit.toFixed(2) + ' in. w.c. rated maximum' + labelNote + '. The duct system is restricting airflow — check the filter, coil, and duct sizing.</span>';
    }
    out.innerHTML = html;
  });
  form.addEventListener('reset', function () { out.innerHTML = ''; });
})();
</script>

## How the TESP calculation works

**Total external static pressure (TESP)** is the total air-side resistance the blower works against — essentially the "blood pressure" of the duct system. You measure it with a manometer at two points and add the magnitudes:

**TESP = |Supply Static Pressure| + |Return Static Pressure|**

The supply side reads **positive** (the blower is pushing) and the return side reads **negative** (the blower is pulling), so you take the absolute value of each and add them together.

### Worked example

Supply static reads **+0.35 in. w.c.** and return static reads **−0.30 in. w.c.**:

**|0.35| + |0.30| = 0.65 in. w.c.**

Against a 0.5 in. w.c. rated maximum, that's **high** — the duct system is too restrictive and airflow is being choked.

## Reading the result

Most residential air handlers and furnaces are rated for a **maximum TESP around 0.5 in. w.c.** Check the specific equipment's data tag for its rating.

| TESP vs. rating | Likely meaning |
|---|---|
| At or below rated | Duct system is within design; blower can move rated airflow |
| Above rated | Restriction — dirty/high-MERV filter, dirty coil, undersized or closed ducts, too few grilles |

> Reading static **across the filter** and **across the coil** separately tells you which component is the biggest restriction. High static almost always shows up as a high [Delta T]({{ "/tools/delta-t-calculator/" | relative_url }}) and low measured [CFM]({{ "/tools/cfm-tonnage-calculator/" | relative_url }}).

## How FieldPad helps in the field

Capture the TESP here, then record it in **FieldPad** against the equipment and the visit. Trend it over maintenance calls to catch a slowly clogging coil, attach it to the job, and turn an airflow finding into a duct-correction estimate — all on one offline record tied to the client.
