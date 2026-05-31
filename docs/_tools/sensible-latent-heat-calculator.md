---
title: "Sensible, Latent & Total Heat Calculator"
short_title: "Sensible & Latent Heat Calculator"
description: "Free psychrometric heat load calculator. Find sensible, latent, and total cooling load in BTU/h from CFM, temperature split, humidity change, and enthalpy change, plus sensible heat ratio."
card_blurb: "Find sensible, latent, and total heat (BTU/h) from CFM and temperature, moisture, or enthalpy change, plus SHR."
tool_category: "Psychrometrics"
tool_order: 1
keywords: ["sensible heat calculator", "latent heat load", "total heat btu calculator", "1.08 0.68 4.5 factor", "sensible heat ratio", "psychrometric load"]
formula_summary: "Qs = 1.08 × CFM × ΔT(°F); Ql = 0.68 × CFM × ΔW(grains/lb); Qt = 4.5 × CFM × Δh(BTU/lb). SHR = Qs ÷ Qt."
how_to_name: "How to calculate sensible, latent, and total heat"
how_to_steps:
  - { name: "Enter the airflow", text: "Enter the airflow across the coil in CFM." }
  - { name: "Enter the changes you measured", text: "Enter the dry-bulb temperature change for sensible heat, the moisture (grains/lb) change for latent heat, and/or the enthalpy change for total heat." }
  - { name: "Apply the factors", text: "Sensible = 1.08 × CFM × ΔT; latent = 0.68 × CFM × Δgrains; total = 4.5 × CFM × Δenthalpy." }
  - { name: "Read the sensible heat ratio", text: "SHR = sensible ÷ total tells you how much of the load is cooling vs. dehumidification." }
faqs:
  - { q: "What are the 1.08, 0.68, and 4.5 factors?", a: "They are standard-air constants at sea level (0.075 lb/ft³). 1.08 = 0.075 × 60 × 0.24 (specific heat) for sensible heat per °F. 0.68 converts CFM and grains-of-moisture change to latent BTU/h. 4.5 = 0.075 × 60 converts CFM and enthalpy change (BTU/lb) to total BTU/h." }
  - { q: "How do you calculate sensible heat?", a: "Sensible heat (BTU/h) = 1.08 × CFM × temperature change in °F. For example, 1,200 CFM across a 20°F drop is 1.08 × 1,200 × 20 = 25,920 BTU/h of sensible cooling." }
  - { q: "What is sensible heat ratio (SHR)?", a: "SHR is the sensible portion of the total load: SHR = sensible heat ÷ total heat. A typical direct-expansion AC system runs an SHR of about 0.75–0.85, meaning 75–85% of its capacity removes temperature and 15–25% removes moisture." }
  - { q: "Do these formulas work at high altitude?", a: "The 1.08, 0.68, and 4.5 factors assume standard air at sea level (0.075 lb/ft³). Above about 2,000 feet the air is less dense, so you should apply an altitude density correction factor for accurate results." }
---

<div class="not-prose bg-white border border-gray-200 rounded-2xl p-6 my-8 shadow-sm">
  <form id="sl-form" class="space-y-5">
    <div>
      <label class="calc-label" for="sl-cfm">Airflow (CFM)</label>
      <input class="calc-field" type="number" inputmode="decimal" step="any" id="sl-cfm" placeholder="e.g. 1200">
    </div>
    <div class="grid grid-cols-1 sm:grid-cols-3 gap-5">
      <div>
        <label class="calc-label" for="sl-dt">ΔT dry-bulb (°F)</label>
        <input class="calc-field" type="number" inputmode="decimal" step="any" id="sl-dt" placeholder="sensible">
      </div>
      <div>
        <label class="calc-label" for="sl-dw">Δ moisture (grains/lb)</label>
        <input class="calc-field" type="number" inputmode="decimal" step="any" id="sl-dw" placeholder="latent">
      </div>
      <div>
        <label class="calc-label" for="sl-dh">Δ enthalpy (BTU/lb)</label>
        <input class="calc-field" type="number" inputmode="decimal" step="any" id="sl-dh" placeholder="total">
      </div>
    </div>
    <div class="flex flex-wrap gap-3 items-center">
      <button class="calc-btn" type="submit">Calculate heat</button>
      <button class="text-sm text-gray-500 hover:text-fieldpad-700" type="reset">Reset</button>
    </div>
  </form>
  <div id="sl-result" class="calc-result mt-5" aria-live="polite"></div>
</div>

<script>
(function () {
  var form = document.getElementById('sl-form');
  var out  = document.getElementById('sl-result');
  if (!form) return;
  form.addEventListener('submit', function (e) {
    e.preventDefault();
    var cfm = parseFloat(document.getElementById('sl-cfm').value);
    var dt  = parseFloat(document.getElementById('sl-dt').value);
    var dw  = parseFloat(document.getElementById('sl-dw').value);
    var dh  = parseFloat(document.getElementById('sl-dh').value);
    if (isNaN(cfm)) { out.innerHTML = '<span class="calc-status calc-status-warn">Enter the airflow in CFM.</span>'; return; }
    if (isNaN(dt) && isNaN(dw) && isNaN(dh)) {
      out.innerHTML = '<span class="calc-status calc-status-warn">Enter at least one change: ΔT, Δ moisture, or Δ enthalpy.</span>';
      return;
    }
    var qs = isNaN(dt) ? null : 1.08 * cfm * dt;
    var ql = isNaN(dw) ? null : 0.68 * cfm * dw;
    var qt = isNaN(dh) ? null : 4.5 * cfm * dh;
    var rows = '';
    if (qs !== null) rows += '<div>Sensible: <strong>' + Math.round(qs).toLocaleString() + ' BTU/h</strong></div>';
    if (ql !== null) rows += '<div>Latent: <strong>' + Math.round(ql).toLocaleString() + ' BTU/h</strong></div>';
    if (qt !== null) rows += '<div>Total: <strong>' + Math.round(qt).toLocaleString() + ' BTU/h</strong></div>';
    // If total not given but sensible+latent are, sum them
    if (qt === null && qs !== null && ql !== null) {
      qt = qs + ql;
      rows += '<div>Total (sensible + latent): <strong>' + Math.round(qt).toLocaleString() + ' BTU/h</strong></div>';
    }
    var note = '';
    if (qs !== null && qt !== null && qt !== 0) {
      note = '<span class="calc-status calc-status-ok">Sensible heat ratio (SHR): <strong>' + (qs / qt).toFixed(2) + '</strong>. Typical DX systems run about 0.75–0.85.</span>';
    } else {
      note = '<span class="calc-status calc-status-ok">Factors assume standard air at sea level. Apply an altitude correction above ~2,000 ft.</span>';
    }
    out.innerHTML = rows + note;
  });
  form.addEventListener('reset', function () { out.innerHTML = ''; });
})();
</script>

## How the heat load calculations work

Cooling and heating loads split into **sensible** (temperature) and **latent** (moisture) heat, which sum to the **total** load. Each has a standard-air formula:

| Component | Formula |
|---|---|
| **Sensible** | Qs = **1.08** × CFM × ΔT (°F) |
| **Latent** | Ql = **0.68** × CFM × Δgrains (grains/lb) |
| **Total** | Qt = **4.5** × CFM × Δh (BTU/lb enthalpy) |

And **Qt = Qs + Ql**.

The constants are all derived from standard air at sea level (0.075 lb/ft³): **1.08** = 0.075 × 60 × 0.24, **4.5** = 0.075 × 60, and **0.68** converts CFM and grains of moisture to latent BTU/h.

### Worked example

**1,200 CFM** across a **20°F** dry-bulb drop:

**Qs = 1.08 × 1,200 × 20 = 25,920 BTU/h** of sensible cooling.

## Sensible heat ratio (SHR)

**SHR = Sensible ÷ Total.** A typical direct-expansion AC system runs an SHR of about **0.75–0.85** — most of the work is lowering temperature, with the rest removing moisture. Humid climates push SHR lower (more latent); dry climates push it higher.

> The factors assume **standard air at sea level**. Above ~2,000 ft, apply an altitude density correction. Need moisture or enthalpy values? Read them from a psychrometric chart or use the [Dew Point & RH Calculator]({{ "/tools/dew-point-calculator/" | relative_url }}); for airflow itself, see the [CFM & Tonnage Calculator]({{ "/tools/cfm-tonnage-calculator/" | relative_url }}).

## How FieldPad helps in the field

Break down the load here, then keep the numbers with the job. **FieldPad** logs your capacity findings against the equipment and the client and turns them into a documented estimate — offline, on your iPhone, with full history on one record.
