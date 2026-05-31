---
title: "Refrigerant PT Chart Calculator"
short_title: "PT Chart (Pressure-Temperature)"
description: "Free refrigerant PT chart lookup. Convert pressure to saturation temperature (or temperature to pressure) for R-410A, R-454B, R-32, R-22, R-134a, R-404A, R-407C, R-448A, and R-449A, with bubble/dew for glide blends."
card_blurb: "Convert pressure to saturation temperature (and back) for 9 common refrigerants, with bubble/dew points for glide blends."
tool_category: "Refrigeration"
tool_order: 6
keywords: ["PT chart", "refrigerant pressure temperature chart", "R-410A PT chart", "R-454B pressure chart", "saturation temperature", "bubble dew point", "refrigerant pressure lookup"]
formula_summary: "Saturation pressure ↔ temperature lookup from published refrigerant PT charts, with linear interpolation. Glide blends report bubble (liquid) and dew (vapor) points."
how_to_name: "How to use a refrigerant PT chart"
how_to_steps:
  - { name: "Select the refrigerant", text: "Pick the exact refrigerant in the system — pressures differ dramatically between refrigerants." }
  - { name: "Choose what you know", text: "Enter a measured pressure to get the saturation temperature, or enter a temperature to get the saturation pressure." }
  - { name: "Read the result", text: "For single-component and near-azeotropic refrigerants you get one value. For glide blends you get a bubble point (use for liquid/subcooling) and a dew point (use for vapor/superheat)." }
  - { name: "Apply it", text: "Subtract from line temperatures to find superheat (dew point at suction pressure) or subcooling (bubble point at liquid pressure)." }
faqs:
  - { q: "What is a refrigerant PT chart?", a: "A pressure-temperature (PT) chart lists the saturation pressure of a refrigerant at each temperature. It lets you convert a gauge pressure to the corresponding boiling/condensing (saturation) temperature, which you need to calculate superheat and subcooling." }
  - { q: "What is the difference between bubble point and dew point?", a: "On a zeotropic blend (like R-407C, R-448A, R-449A), the refrigerant boils and condenses across a range of temperatures called glide. The bubble point is the saturated-liquid temperature (use it for subcooling on the liquid line); the dew point is the saturated-vapor temperature (use it for superheat on the suction line). Single-component refrigerants like R-22, R-32, and R-134a have no glide, so bubble and dew are the same." }
  - { q: "How do I find superheat and subcooling from a PT chart?", a: "For superheat: convert the suction pressure to a saturation temperature using the DEW point, then subtract it from the measured suction line temperature. For subcooling: convert the liquid pressure to a saturation temperature using the BUBBLE point, then subtract the liquid line temperature from it." }
  - { q: "Why are the pressures so different between refrigerants?", a: "Each refrigerant has its own pressure-temperature relationship. For example, at 40°F, R-22 is about 69 PSIG, R-410A is about 118 PSIG, and R-32 is about 121 PSIG. Using the wrong refrigerant's chart will give wrong superheat/subcooling and can lead to a serious mischarge." }
---

<div class="not-prose bg-white border border-gray-200 rounded-2xl p-6 my-8 shadow-sm">
  <div class="calc-status calc-status-warn" style="margin-top:0;margin-bottom:1.25rem;">
    <strong>Reference tool.</strong> Data is interpolated from published manufacturer PT charts. Always confirm against a calibrated gauge and the equipment's service documentation. Glide blends (R-407C, R-448A, R-449A) show both bubble and dew points — use the right one. If readings don't match the chart, suspect contamination or a blend that has fractionated, and stop before charging.
  </div>
  <form id="pt-form" class="grid grid-cols-1 sm:grid-cols-3 gap-5">
    <div>
      <label class="calc-label" for="pt-ref">Refrigerant</label>
      <select class="calc-field" id="pt-ref">
        <option value="R-410A">R-410A</option>
        <option value="R-454B">R-454B (A2L)</option>
        <option value="R-32">R-32 (A2L)</option>
        <option value="R-22">R-22</option>
        <option value="R-134a">R-134a</option>
        <option value="R-404A">R-404A</option>
        <option value="R-407C">R-407C (glide)</option>
        <option value="R-448A">R-448A (glide)</option>
        <option value="R-449A">R-449A (glide)</option>
      </select>
    </div>
    <div>
      <label class="calc-label" for="pt-mode">I know the…</label>
      <select class="calc-field" id="pt-mode">
        <option value="pressure">Pressure (find temp)</option>
        <option value="temp">Temperature (find pressure)</option>
      </select>
    </div>
    <div>
      <label class="calc-label" for="pt-val"><span id="pt-val-label">Pressure (PSIG)</span></label>
      <input class="calc-field" type="number" inputmode="decimal" step="any" id="pt-val" placeholder="e.g. 118">
    </div>
    <div class="sm:col-span-3 flex flex-wrap gap-3 items-center">
      <button class="calc-btn" type="submit">Look up</button>
      <button class="text-sm text-gray-500 hover:text-fieldpad-700" type="reset">Reset</button>
    </div>
  </form>
  <div id="pt-result" class="calc-result mt-5" aria-live="polite"></div>
</div>

<script>
(function () {
  // Saturation data transcribed from published manufacturer PT charts
  // (Hudson Technologies; R-454B from Honeywell Solstice). Pressures in PSIG.
  // Vacuum readings converted from inches Hg to negative PSIG (1 inHg = 0.4912 psi).
  function grid(arr) { return arr.map(function (p, i) { return [-40 + i * 5, p]; }); }
  var DATA = {
    'R-410A': { glide: false, sat: grid([11.6,14.9,18.5,22.5,26.9,31.7,36.8,42.5,48.6,55.2,62.3,70.0,78.3,87.3,96.8,107.0,118.0,130.0,142.0,155.0,170.0,185.0,201.0,217.0,235.0,254.0,274.0,295.0,317.0,340.0,365.0,391.0,418.0,446.0,476.0,507.0,539.0,573.0,608.0]) },
    'R-32':   { glide: false, sat: grid([11.0,14.4,18.2,22.3,26.8,31.7,37.1,42.9,49.3,56.1,63.5,71.5,80.0,89.2,99.1,109.7,121.0,133.0,145.9,159.5,174.1,189.5,205.8,223.2,241.5,260.9,281.3,302.9,325.7,349.6,374.9,401.4,429.3,458.6,489.4,521.8,555.7,591.4,628.8]) },
    'R-22':   { glide: false, sat: grid([0.5,2.6,4.9,7.4,10.1,13.2,16.5,20.1,24.0,28.2,32.8,37.7,43.0,48.8,54.9,61.5,68.5,76.0,84.0,92.6,102.0,111.0,121.0,132.0,144.0,156.0,168.0,182.0,196.0,211.0,226.0,243.0,260.0,278.0,297.0,317.0,337.0,359.0,382.0]) },
    'R-134a': { glide: false, sat: grid([-7.27,-6.14,-4.86,-3.39,-1.82,0.6,1.9,4.0,6.5,9.1,11.9,15.0,18.4,22.1,26.1,30.4,35.0,40.1,45.5,51.3,57.5,64.1,71.2,78.8,86.8,95.4,104.0,114.0,124.0,135.0,147.0,159.0,171.0,185.0,199.0,214.0,229.0,246.0,263.0]) },
    'R-404A': { glide: false, sat: grid([4.3,6.8,9.5,12.5,15.7,19.3,23.2,27.5,32.1,37.0,42.4,48.2,54.5,61.2,68.4,76.1,84.4,93.2,103.0,113.0,123.0,135.0,147.0,159.0,173.0,187.0,202.0,218.0,234.0,252.0,270.0,289.0,310.0,331.0,353.0,377.0,401.0,426.0,453.0]) },
    'R-407C': { glide: true,
      bubble: grid([3.0,5.4,8.0,10.9,14.1,17.6,21.3,25.4,29.9,34.7,39.9,45.6,51.6,58.2,65.2,72.6,80.7,89.2,98.3,108.0,118.0,129.0,141.0,153.0,166.0,180.0,195.0,210.0,226.0,243.0,261.0,280.0,300.0,321.0,342.0,365.0,389.0]),
      dew:    grid([-2.16,0.6,1.8,4.1,6.6,9.4,12.5,15.9,19.6,23.6,28.0,32.8,38.0,43.6,49.6,56.1,63.1,70.6,78.7,87.3,96.8,106.0,117.0,128.0,140.0,153.0,166.0,181.0,196.0,211.0,229.0,247.0,266.0,286.0,307.0,329.0,353.0]) },
    'R-448A': { glide: true,
      bubble: grid([4.9,7.5,10.4,13.5,17.0,20.8,24.9,29.3,34.2,39.4,45.1,51.2,57.8,64.8,72.3,80.4,89.0,98.2,108.0,118.0,129.0,141.0,154.0,167.0,181.0,195.0,211.0,227.0,244.0,262.0,281.0,300.0,321.0,343.0,365.0,389.0,414.0,440.0,467.0]),
      dew:    grid([0.0,2.1,4.4,7.0,9.8,13.0,16.4,20.2,24.3,28.8,33.6,38.9,44.6,50.7,57.3,64.4,72.1,80.3,89.0,98.4,108.0,119.0,130.0,142.0,155.0,169.0,183.0,198.0,214.0,231.0,249.0,268.0,288.0,309.0,331.0,355.0,379.0,405.0,433.0]) },
    'R-449A': { glide: true,
      bubble: grid([4.8,7.4,10.2,13.3,16.7,20.5,24.6,29.0,33.8,39.0,44.6,50.6,57.1,64.0,71.5,79.5,88.0,97.1,107.0,117.0,128.0,140.0,152.0,165.0,179.0,193.0,208.0,224.0,241.0,259.0,278.0,297.0,318.0,339.0,362.0,385.0,410.0,435.0,462.0]),
      dew:    grid([0.0,2.0,4.4,6.9,9.8,12.9,16.3,20.1,24.1,28.6,33.4,38.6,44.3,50.4,56.9,64.0,71.6,79.7,88.4,97.7,108.0,118.0,129.0,141.0,154.0,167.0,182.0,197.0,213.0,229.0,247.0,266.0,286.0,307.0,329.0,352.0,376.0,402.0,429.0]) },
    'R-454B': { glide: true,
      bubble: [[-59.0,0],[-25.2,20],[-4.6,40],[10.9,60],[17.4,70],[23.4,80],[29.0,90],[34.1,100],[38.9,110],[43.5,120],[47.7,130],[51.8,140],[55.7,150],[59.4,160],[63.0,170],[66.4,180],[69.6,190],[72.8,200],[75.9,210],[78.8,220],[84.5,240],[89.8,260],[94.8,280],[99.6,300],[104.2,320],[108.5,340],[112.7,360]],
      dew:    [[-57.3,0],[-23.3,20],[-2.6,40],[13.0,60],[19.6,70],[25.6,80],[31.1,90],[36.3,100],[41.1,110],[45.7,120],[50.0,130],[54.1,140],[58.0,150],[61.7,160],[65.2,170],[68.6,180],[71.9,190],[75.1,200],[78.1,210],[81.1,220],[86.7,240],[92.0,260],[97.0,280],[101.8,300],[106.3,320],[110.7,340],[114.8,360]] }
  };

  function interpByX(pairs, x) { // x = temp, return pressure
    var n = pairs.length;
    if (x <= pairs[0][0]) return { v: pairs[0][1], edge: x < pairs[0][0] };
    if (x >= pairs[n - 1][0]) return { v: pairs[n - 1][1], edge: x > pairs[n - 1][0] };
    for (var i = 1; i < n; i++) {
      if (x <= pairs[i][0]) {
        var a = pairs[i - 1], b = pairs[i], f = (x - a[0]) / (b[0] - a[0]);
        return { v: a[1] + f * (b[1] - a[1]), edge: false };
      }
    }
  }
  function interpByY(pairs, y) { // y = pressure, return temp
    var n = pairs.length;
    if (y <= pairs[0][1]) return { v: pairs[0][0], edge: y < pairs[0][1] };
    if (y >= pairs[n - 1][1]) return { v: pairs[n - 1][0], edge: y > pairs[n - 1][1] };
    for (var i = 1; i < n; i++) {
      if (y <= pairs[i][1]) {
        var a = pairs[i - 1], b = pairs[i], f = (y - a[1]) / (b[1] - a[1]);
        return { v: a[0] + f * (b[0] - a[0]), edge: false };
      }
    }
  }
  function fmtP(p) {
    if (p < 0) return (Math.abs(p) / 0.4912).toFixed(1) + ' in Hg vacuum';
    return p.toFixed(1) + ' PSIG';
  }

  var form  = document.getElementById('pt-form');
  var out   = document.getElementById('pt-result');
  var modeEl = document.getElementById('pt-mode');
  var labelEl = document.getElementById('pt-val-label');
  var valEl = document.getElementById('pt-val');
  if (!form) return;

  function syncLabel() {
    if (modeEl.value === 'temp') { labelEl.textContent = 'Temperature (°F)'; valEl.placeholder = 'e.g. 40'; }
    else { labelEl.textContent = 'Pressure (PSIG)'; valEl.placeholder = 'e.g. 118'; }
  }
  modeEl.addEventListener('change', syncLabel);

  form.addEventListener('submit', function (e) {
    e.preventDefault();
    var ref = document.getElementById('pt-ref').value;
    var mode = modeEl.value;
    var v = parseFloat(valEl.value);
    var d = DATA[ref];
    if (isNaN(v)) { out.innerHTML = '<span class="calc-status calc-status-warn">Enter a value.</span>'; return; }

    var html = '', edge = false;
    if (mode === 'temp') {
      if (!d.glide) {
        var r = interpByX(d.sat, v); edge = r.edge;
        html = 'Saturation pressure: <span class="calc-result-value">' + fmtP(r.v) + '</span>';
      } else {
        var rb = interpByX(d.bubble, v), rd = interpByX(d.dew, v); edge = rb.edge || rd.edge;
        html = '<div>Bubble (liquid): <strong>' + fmtP(rb.v) + '</strong></div><div>Dew (vapor): <strong>' + fmtP(rd.v) + '</strong></div>';
      }
    } else {
      if (!d.glide) {
        var rt = interpByY(d.sat, v); edge = rt.edge;
        html = 'Saturation temperature: <span class="calc-result-value">' + rt.v.toFixed(1) + ' &deg;F</span>';
      } else {
        var tb = interpByY(d.bubble, v), td = interpByY(d.dew, v); edge = tb.edge || td.edge;
        html = '<div>Bubble temp (for subcooling, liquid line): <strong>' + tb.v.toFixed(1) + ' &deg;F</strong></div>' +
               '<div>Dew temp (for superheat, suction line): <strong>' + td.v.toFixed(1) + ' &deg;F</strong></div>';
      }
    }
    if (edge) html += '<span class="calc-status calc-status-warn">Value is outside the published chart range — result is clamped to the nearest charted point. Verify with full manufacturer data.</span>';
    else html += '<span class="calc-status calc-status-ok">Interpolated from published ' + ref + ' chart data. Verify against a calibrated gauge.</span>';
    out.innerHTML = html;
  });
  form.addEventListener('reset', function () { out.innerHTML = ''; setTimeout(syncLabel, 0); });
  syncLabel();
})();
</script>

## How to read a pressure-temperature chart

A **PT chart** maps a refrigerant's **saturation pressure** to its **saturation temperature** — the point where it's boiling/condensing. That conversion is the foundation of charging and diagnostics: you read a gauge pressure, convert it to a saturation temperature, and compare it to a measured line temperature to get **superheat** or **subcooling**.

- **Superheat** = suction line temperature − saturation temperature at suction pressure (use the **dew** point on a blend).
- **Subcooling** = saturation temperature at liquid pressure − liquid line temperature (use the **bubble** point on a blend).

### Why the refrigerant matters

Each refrigerant has a completely different pressure curve. At **40°F**, for example:

| Refrigerant | ~Saturation pressure at 40°F |
|---|---|
| R-22 | 69 PSIG |
| R-134a | 35 PSIG |
| R-404A | 84 PSIG |
| R-410A | 118 PSIG |
| R-32 | 121 PSIG |
| R-454B | ~80 PSIG (bubble) |

Reading the wrong refrigerant's chart will throw off superheat and subcooling and can cause a serious mischarge.

## Glide blends: bubble vs. dew

**Zeotropic blends** boil and condense across a temperature range called **glide**. They have two saturation points at any pressure:

- **Bubble point** (saturated liquid) — use for **subcooling** on the liquid line.
- **Dew point** (saturated vapor) — use for **superheat** on the suction line.

This tool reports both for the glide blends (**R-407C**, **R-448A**, **R-449A**, and the small-glide **R-454B**). Single-component and near-azeotropic refrigerants (**R-22**, **R-32**, **R-134a**, **R-404A**, **R-410A**) have effectively no glide — bubble and dew are the same.

> Pair this chart with the [Superheat]({{ "/tools/superheat-calculator/" | relative_url }}) and [Subcooling]({{ "/tools/subcooling-calculator/" | relative_url }}) calculators to finish the diagnosis.

## How FieldPad helps in the field

Look up the saturation temperature here, then keep the readings with the equipment. **FieldPad** logs your pressures, line temperatures, superheat, and subcooling against the system and the client, attaches them to the job, and rolls the work into a signed invoice — offline, on your iPhone, with the full charging history on one record.
