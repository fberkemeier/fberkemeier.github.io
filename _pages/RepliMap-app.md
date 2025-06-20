---

layout: page
title: RepliMap interactive app
permalink: /RepliMap-app/
description: A mathematical map of DNA replication kinetics - Find a suitable equation for your research needs.
nav: false
nav_order: 8
horizontal: false

---

#### Model assumptions ####

<style>
/* Theme-aware dropdown background */
@media (prefers-color-scheme: dark) {
    :root {
        --dropdown-background-color: #222;
    }
}

@media (prefers-color-scheme: light) {
    :root {
        --dropdown-background-color: #fff;
    }
}

/* Existing styling for native selects */
select {
    background-color: inherit;
    color: inherit;
}

/* Fix horizontal shift */
html {
    overflow-y: scroll;
}
</style>

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
            <option value="constant">Constant</option>
        </select>
    </div>
</div>

<!-- Box for I and v equations -->
<div style="border: 2px solid #ccc; border-radius: 8px; padding: 15px; margin-top: 20px; font-size: 1.3em; margin-bottom: 30px;">
    <div id="equationDiv">
        $$ I(x,t) = I_0 \quad , \quad v(t) = v_0 $$
    </div>
</div>

#### Quantity to display ####

<!-- Custom dropdown for Quantity -->
<div id="quantityDropdown" style="position: relative; display: inline-block; margin-top: 0px;">
    <div id="quantityDropdownButton" onclick="toggleQuantityDropdown()" 
        style="border: 1px solid #ccc; border-radius: 4px; padding: 10px; min-width: 280px; cursor: pointer; background-color: inherit; color: inherit; position: relative;">
        <span id="quantityDropdownButtonContent">Replicated fraction - \( f(x,t) \)</span>
        <span style="position: absolute; right: 10px; top: 50%; transform: translateY(-50%); pointer-events: none;">&#9662;</span>
    </div>
    <div id="quantityDropdownList" style="display: none; position: absolute; z-index: 1000; background-color: var(--dropdown-background-color, white); color: inherit; border: 1px solid #ccc; border-radius: 4px; margin-top: 2px; width: 100%;">
        <div class="quantityOption" data-value="replication_fraction" onclick="selectQuantityOption(this)" style="padding: 10px; cursor: pointer;">
            <span>Replicated fraction - \( f(x,t) \)</span>
        </div>
        <div class="quantityOption" data-value="expected_replication_timing" onclick="selectQuantityOption(this)" style="padding: 10px; cursor: pointer;">
            <span>Expected replication timing - \( T(x) \)</span>
        </div>
    </div>
</div>

<!-- Hidden variable for selected quantity -->
<input type="hidden" id="quantitySelectValue" value="replication_fraction">

<!-- Box for quantity equation -->
<div style="border: 2px solid #ccc; border-radius: 8px; padding: 15px; margin-top: 20px; font-size: 1.3em;">
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

    <!-- Box for variation equations (hidden unless checkbox is checked) -->
    <div id="variationBox" style="display: none; border: 2px solid #ccc; border-radius: 8px; padding: 15px; margin-top: 10px; font-size: 1.3em;">
        <div id="variationDiv"></div>
    </div>
</div>

<script>
    const initEqMap = {
        'space_time': 'I(x,t) = I(x,t)',
        'time_homogeneous': 'I(x,t) = I(x)',
        'constant': 'I(x,t) = I_0'
    };

    const forkEqMap = {
        'space_time': 'v(x,t) = v(x,t)',
        'constant': 'v(x,t) = v_0'
    };

    const equations = {
        replication_fraction_space_time_space_time: '$$ f(x,t) = 1 - \\exp\\left( - \\iint_{\\Lambda_X[v]} I(\\xi,\\tau) \\, d\\xi \\, d\\tau \\right) $$',
        replication_fraction_space_time_time_homogeneous: '$$ f(x,t) = 1 - \\exp\\left( - \\iint_{\\Lambda_X[v]} I(\\xi) \\, d\\xi \\, d\\tau \\right) $$',
        replication_fraction_space_time_constant: '$$ f(x,t) = 1 - \\exp\\left( - I_0 \\, \\text{Vol}(\\Lambda_X[v]) \\right) $$',
        replication_fraction_constant_space_time: '$$ f(x,t) = 1 - \\exp\\left( - \\int_0^t \\int_{x - v_0 \\tau}^{x + v_0 \\tau} I(\\xi,\\tau) \\, d\\xi \\, d\\tau \\right) $$',
        replication_fraction_constant_time_homogeneous: '$$ f(x,t) = 1 - \\exp\\left( - \\int_0^t \\int_{x - v_0 \\tau}^{x + v_0 \\tau} I(\\xi) \\, d\\xi \\, d\\tau \\right) $$',
        replication_fraction_constant_constant: '$$ f(x,t) = 1 - \\exp\\left( - I_0 v_0 t^2 \\right) $$',

        expected_replication_timing_space_time_space_time: '$$ T(x) = \\int_0^\\infty \\exp\\left( - \\iint_{\\Lambda_X[v]} I(\\xi,\\tau) \\, d\\xi \\, d\\tau \\right) \\, dt $$',
        expected_replication_timing_space_time_time_homogeneous: '$$ T(x) = \\int_0^\\infty \\exp\\left( - \\iint_{\\Lambda_X[v]} I(\\xi) \\, d\\xi \\, d\\tau \\right) \\, dt $$',
        expected_replication_timing_space_time_constant: '$$ T(x) =  \\int_0^\\infty \\exp\\left( - I_0 \\, \\text{Vol}(\\Lambda_X[v]) \\right) \\, dt $$',
        expected_replication_timing_constant_space_time: '$$ T(x) = \\int_0^\\infty \\exp\\left( - \\int_0^t \\int_{x - v_0 \\tau}^{x + v_0 \\tau} I(\\xi,\\tau) \\, d\\xi \\, d\\tau \\right) \\, dt $$',
        expected_replication_timing_constant_time_homogeneous: '$$ T(x) = \\int_0^\\infty \\exp\\left( - \\int_0^t \\int_{x - v_0 \\tau}^{x + v_0 \\tau} I(\\xi) \\, d\\xi \\, d\\tau \\right) \\, dt $$',
        expected_replication_timing_constant_constant: '$$ T(x) = \\frac{1}{2}\\sqrt{\\frac{\\pi}{I_0 v_0}} $$'
    };

    const quantityEqMap = {
        replication_fraction: {
            'space_time_space_time': 'replication_fraction_space_time_space_time',
            'space_time_time_homogeneous': 'replication_fraction_space_time_time_homogeneous',
            'space_time_constant': 'replication_fraction_space_time_constant',
            'constant_space_time': 'replication_fraction_constant_space_time',
            'constant_time_homogeneous': 'replication_fraction_constant_time_homogeneous',
            'constant_constant': 'replication_fraction_constant_constant'
        },
        expected_replication_timing: {
            'space_time_space_time': 'expected_replication_timing_space_time_space_time',
            'space_time_time_homogeneous': 'expected_replication_timing_space_time_time_homogeneous',
            'space_time_constant': 'expected_replication_timing_space_time_constant',
            'constant_space_time': 'expected_replication_timing_constant_space_time',
            'constant_time_homogeneous': 'expected_replication_timing_constant_time_homogeneous',
            'constant_constant': 'expected_replication_timing_constant_constant'
        }
    };

    const variationEqMap = {
        'constant_time_homogeneous_replication_fraction': [
            'replication_fraction_constant_time_homogeneous_variation_1',
            'replication_fraction_constant_time_homogeneous_variation_2',
            'replication_fraction_constant_time_homogeneous_variation_3'
        ],
        'space_time_space_time_expected_replication_timing': [
            'expected_replication_timing_space_time_space_time_variation_1',
            'expected_replication_timing_space_time_space_time_variation_2'
        ]
    };

    const variationEquations = {
        replication_fraction_constant_time_homogeneous_variation_1: '$$ f(x,t) = 1 - \\exp\\left( - \\int_{-v_0 t}^{v_0 t} \\left(t - \\tfrac{|\\xi|}{v_0}\\right)I(x + \\xi)\, d\\xi \\right) $$',
        replication_fraction_constant_time_homogeneous_variation_2: '$$ f(x,t) = 1 - \\exp\\left( - (\\phi_t \\ast I)(x) \\right) $$',
        replication_fraction_constant_time_homogeneous_variation_3: '$$ \\phi_t(x) = \\begin{cases} t - \\tfrac{|\\xi|}{v_0}, & \\text{if }|\\xi|\\le v_0 t\\\\ 0, & \\text{if }|\\xi|> v_0 t.\\end{cases} $$',
        expected_replication_timing_space_time_space_time_variation_1: '$$ T(x) = \\int_0^\\infty \\left( 1 - f(x,t) \\right) dt $$',
        expected_replication_timing_space_time_space_time_variation_2: '$$ \\Lambda(x,t) = \\iint_{\\Lambda_X[v]} I(\\xi,\\tau) \\, d\\xi \\, d\\tau $$'
    };

    function updateEquations() {
        var forkSpeed = document.getElementById('forkSpeedSelect').value;
        var initiationRate = document.getElementById('initiationRateSelect').value;

        var initEq = initEqMap[initiationRate];
        var forkEq = forkEqMap[forkSpeed];
        var eqDiv = document.getElementById('equationDiv');
        eqDiv.textContent = '$$ ' + initEq + ' \\quad , \\quad ' + forkEq + ' $$';
        if (typeof MathJax !== 'undefined') {
            MathJax.typesetPromise([eqDiv]);
        }

        updateQuantityEquation();
    }

    function updateQuantityEquation() {
        var forkSpeed = document.getElementById('forkSpeedSelect').value;
        var initiationRate = document.getElementById('initiationRateSelect').value;
        var quantityKey = forkSpeed + '_' + initiationRate;
        var selectedQuantity = document.getElementById('quantitySelectValue').value;

        var quantityDiv = document.getElementById('quantityEquationDiv');
        var eqLabel = (quantityEqMap[selectedQuantity] && quantityEqMap[selectedQuantity][quantityKey]) || '';
        var quantityEq = equations[eqLabel] || '';

        quantityDiv.textContent = quantityEq;
        if (typeof MathJax !== 'undefined') {
            MathJax.typesetPromise([quantityDiv]);
        }

        updateVariationSection();
    }

    function updateVariationSection() {
        var forkSpeed = document.getElementById('forkSpeedSelect').value;
        var initiationRate = document.getElementById('initiationRateSelect').value;
        var selectedQuantity = document.getElementById('quantitySelectValue').value;
        var variationSection = document.getElementById('variationSection');
        var variationBox = document.getElementById('variationBox');
        var variationDiv = document.getElementById('variationDiv');

        var comboKey = forkSpeed + '_' + initiationRate + '_' + selectedQuantity;

        if (variationEqMap[comboKey]) {
            variationSection.style.display = 'block';
            variationBox.style.display = 'none';
            variationDiv.innerHTML = '';
            document.getElementById('variationCheckbox').checked = false;
        } else {
            variationSection.style.display = 'none';
        }
    }

    function updateVariationText() {
        var isChecked = document.getElementById('variationCheckbox').checked;
        var variationBox = document.getElementById('variationBox');
        var variationDiv = document.getElementById('variationDiv');

        var forkSpeed = document.getElementById('forkSpeedSelect').value;
        var initiationRate = document.getElementById('initiationRateSelect').value;
        var selectedQuantity = document.getElementById('quantitySelectValue').value;
        var comboKey = forkSpeed + '_' + initiationRate + '_' + selectedQuantity;

        if (isChecked && variationEqMap[comboKey]) {
            variationBox.style.display = 'block';
            var eqLabels = variationEqMap[comboKey];
            variationDiv.innerHTML = eqLabels.map(label => variationEquations[label] || '').join('');
        } else {
            variationBox.style.display = 'none';
            variationDiv.innerHTML = '';
        }

        if (typeof MathJax !== 'undefined') {
            MathJax.typesetPromise([variationDiv]);
        }
    }

    function toggleQuantityDropdown() {
        var list = document.getElementById('quantityDropdownList');
        var isVisible = (list.style.display === 'block');
        closeAllDropdowns();
        list.style.display = isVisible ? 'none' : 'block';

        if (!isVisible && typeof MathJax !== 'undefined') {
            MathJax.typesetPromise([list]);
        }
    }

    function selectQuantityOption(element) {
        var buttonContent = document.getElementById('quantityDropdownButtonContent');
        buttonContent.innerHTML = element.querySelector('span').innerHTML;

        document.getElementById('quantitySelectValue').value = element.getAttribute('data-value');
        document.getElementById('quantityDropdownList').style.display = 'none';

        updateQuantityEquation();

        if (typeof MathJax !== 'undefined') {
            MathJax.typesetPromise([buttonContent]);
        }
    }

    document.addEventListener('click', function(event) {
        var dropdown = document.getElementById('quantityDropdown');
        if (!dropdown.contains(event.target)) {
            document.getElementById('quantityDropdownList').style.display = 'none';
        }
    });

    function closeAllDropdowns() {
        document.getElementById('quantityDropdownList').style.display = 'none';
    }

    document.addEventListener('DOMContentLoaded', updateEquations);
</script>
