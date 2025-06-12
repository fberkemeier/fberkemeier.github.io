---
layout: distillpage
title: RepliMap
description: Toolkit for simulating, analysing, and inferring DNA replication kinetics.
img: assets/img/research_preview/replifit.jpg
importance: 2
category: software
bibliography: papers.bib

---
<!---<sub>[← Research](/projects/)</sub>-->

We introduce [RepliMap](https://github.com/fberkemeier/DNA_replication_model), a comprehensive toolkit for analysing DNA replication timing, origin firing rates, and genomic stability across cell lines and chromosomal regions. It includes functions for loading and processing data across whole-genome regions, telomeres, centromeres, and specific loci of interest. By fitting origin firing rates to replication timing data, the toolkit efficiently predicts and compares experimental and modelled timing profiles <d-cite key="berkemeier2025dna"></d-cite>. The resulting error distributions between predicted and experimental data help in pinpointing regions of interest.


<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/research/replifit.png" title="RepliFit" class="img-fluid rounded z-depth-1" %}
        <div class="caption" style="text-align: center; font-style: italic; margin-top: 10px;">
            Fitting Repi-seq timing data with a stochastic model of replication.
        </div>
    </div>
</div>

Test the [RepliMap interactive app](/RepliMap-app/) (under development).


<sub>[← Research](/projects/)</sub>