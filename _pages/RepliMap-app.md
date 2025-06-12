---

layout: page
title: RepliMap interactive app
permalink: /RepliMap-app/
description: Mapping DNA replication kinetics - Find a suitable equation for your research needs.
nav: false
nav_order: 8
horizontal: false

---

#### Model assumptions

<div style="display: flex; gap: 20px; flex-wrap: wrap; align-items: center; margin-bottom: 20px;">
    <div>
        <label for="initiationRateSelect">Initiation rate:</label><br>
        <select id="initiationRateSelect" onchange="updateEquations()">
            <option value="space_time">Space-time dependent</option>
            <option value="time_homogeneous">Time-homogeneous</option>
            <option value="constant">Constant</option>
        </select>
    </div>
    <div>
        <label for="forkSpeedSelect">Fork speed:</label><br>
        <select id="forkSpeedSelect" onchange="updateEquations()">
            <option value="space_time">Space-time dependent</option>
            <option value="homogeneous">Constant</option>
        </select>
    </div>
</div>

<!-- Box for I and v equations -->
<div style="border: 2px solid #ccc; border-radius: 8px; padding: 15px; margin-top: 20px; font-size: 1.5em; margin-bottom: 30px;">
    <div id="equationDiv">
        $$ I(x,t) = I_0 \quad , \quad v(t) = v_0 $$
    </div>
</div>

#### Quantity to display

<!-- Third dropdown below equations (still present) -->
<div style="margin-top: 0px;">
    <select id="quantitySelect" onchange="updateQuantityEquation()">
        <option value="replication_fraction">Replication fraction</option>
    </select>
</div>

<!-- Box for quantity equation -->
<div style="border: 2px solid #ccc; border-radius: 8px; padding: 15px; margin-top: 20px; font-size: 1.5em;">
    <div id="quantityEquationDiv">
        $$ f(x,t) = 1 - \exp\left( - \int_0^t I(x,\tau) \, d\tau \right) $$
    </div>
</div>

<!-- Variations section, hidden initially -->
<div id="variationSection" style="display: none; margin-top: 20px;">
    <!-- Checkbox for variations -->
    <div>
        <label>
            <input type="checkbox" id="variationCheckbox" onchange="updateVariationText()">
            Show variations
        </label>
    </div>

    <!-- Box for variation equation (hidden unless checkbox is checked) -->
    <div id="variationBox" style="display: none; border: 2px solid #ccc; border-radius: 8px; padding: 15px; margin-top: 10px; font-size: 1.5em;">
        <div id="variationDiv"></div>
    </div>
</div>

<script>
    // Lookup table for initiation rate equations
    const initEqMap = {
        'space_time': 'I(x,t) = I(x,t)',
        'time_homogeneous': 'I(x,t) = I(x)',
        'constant': 'I(x,t) = I_0'
    };
    // Lookup table for fork speed equations
    const forkEqMap = {
        'space_time': 'v(x,t) = v(x,t)',
        'homogeneous': 'v(x,t) = v_0'
    };

    // Lookup table for replication fraction equation depending on fork + init choices
    const quantityEqMap = {
        'space_time_space_time': '$$ f(x,t) = 1 - \\exp\\left( - \\iint_{\\Lambda_X[v]} I(\\xi,\\tau) \\, d\\xi \\, d\\tau \\right) $$',
        'space_time_time_homogeneous': '$$ f(x,t) = 1 - \\exp\\left( - \\iint_{\\Lambda_X[v]} I(\\xi) \\, d\\xi \\, d\\tau \\right) $$',
        'space_time_constant': '$$ f(x,t) = 1 - \\exp\\left( - I_0 \\, \\text{Vol}(\\Lambda_X[v]) \\right) $$',
        'homogeneous_space_time': '$$ f(x,t) = 1 - \\exp\\left( - \\int_0^t \\int_{x - v_0 \\tau}^{x + v_0 \\tau} I(\\xi,\\tau) \\, d\\xi \\, d\\tau \\right) $$',
        'homogeneous_time_homogeneous': '$$ f(x,t) = 1 - \\exp\\left( - \\int_0^t \\int_{x - v_0 \\tau}^{x + v_0 \\tau} I(\\xi) \\, d\\xi \\, d\\tau \\right) $$',
        'homogeneous_constant': '$$ f(x,t) = 1 - \\exp\\left( - I_0 v_0 t^2 \\right) $$'
    };

    function updateEquations() {
        var forkSpeed = document.getElementById('forkSpeedSelect').value;
        var initiationRate = document.getElementById('initiationRateSelect').value;

        // Update I and v equation
        var initEq = initEqMap[initiationRate];
        var forkEq = forkEqMap[forkSpeed];
        var eqDiv = document.getElementById('equationDiv');
        eqDiv.textContent = '$$ ' + initEq + ' \\quad , \\quad ' + forkEq + ' $$';
        if (typeof MathJax !== 'undefined') {
            MathJax.typesetPromise([eqDiv]);
        }

        // Also update quantity equation accordingly
        updateQuantityEquation();
    }

    function updateQuantityEquation() {
        var forkSpeed = document.getElementById('forkSpeedSelect').value;
        var initiationRate = document.getElementById('initiationRateSelect').value;
        var quantityKey = forkSpeed + '_' + initiationRate;

        var quantityDiv = document.getElementById('quantityEquationDiv');
        var quantityEq = quantityEqMap[quantityKey] || ''; // fallback

        quantityDiv.textContent = quantityEq;
        if (typeof MathJax !== 'undefined') {
            MathJax.typesetPromise([quantityDiv]);
        }

        // Also update variation section visibility
        updateVariationSection();
    }

    function updateVariationSection() {
        var forkSpeed = document.getElementById('forkSpeedSelect').value;
        var initiationRate = document.getElementById('initiationRateSelect').value;
        var variationSection = document.getElementById('variationSection');
        var variationBox = document.getElementById('variationBox');
        var variationDiv = document.getElementById('variationDiv');

        if (forkSpeed === 'homogeneous' && initiationRate === 'time_homogeneous') {
            variationSection.style.display = 'block';
            variationBox.style.display = 'none'; // initially hide box
            variationDiv.innerHTML = ''; // clear content
            document.getElementById('variationCheckbox').checked = false;
        } else {
            variationSection.style.display = 'none';
        }
    }

    function updateVariationText() {
        var isChecked = document.getElementById('variationCheckbox').checked;
        var variationBox = document.getElementById('variationBox');
        var variationDiv = document.getElementById('variationDiv');

        if (isChecked) {
            variationBox.style.display = 'block';
            variationDiv.innerHTML =
                  '$$ f(x,t) = 1 - \\exp\\left( - \\int_{-v_0 t}^{v_0 t} \\left(t - \\tfrac{|\\xi|}{v_0}\\right)I(x + \\xi)\, d\\xi \\right) $$' +
                  '$$ f(x,t) = 1 - \\exp\\left( - (\\phi_t \\ast I)(x) \\right) $$' +
                  '$$ \\phi_t(x) = \\begin{cases} t - \\tfrac{|\\xi|}{v_0}, & \\text{if }|\\xi|\\le v_0 t\\\\ 0, & \\text{if }|\\xi|> v_0 t.\\end{cases} $$';
        } else {
            variationBox.style.display = 'none';
            variationDiv.innerHTML = '';
        }

        if (typeof MathJax !== 'undefined') {
            MathJax.typesetPromise([variationDiv]);
        }
    }

    // Initialise equations on first load
    document.addEventListener('DOMContentLoaded', updateEquations);
</script>
