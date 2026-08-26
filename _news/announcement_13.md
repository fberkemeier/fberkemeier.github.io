---
layout: post
title: "Introducing RepliCanvas: a visual playground for DNA replication"
date: 2026-08-26 10:00:00+0100
inline: false
related_posts: false
---

For years, I have wanted a simple way to draw DNA replication without piecing together every strand, origin, and fork by hand. [RepliCanvas](https://fberkemeier.github.io/RepliCanvas/) grew out of that idea: a small, open-source side project and browser playground for drawing and animating DNA replication.

RepliCanvas allows you to place origins, shape replication bubbles, move individual forks, introduce strand breaks, and watch the molecule progress through S phase. DNA can be linear, circular, or drawn along a completely free-form path.

There are plenty of knobs to play with, including strand geometry, base-pair detail, colours, labels, contours, and fork transitions. The result can be exported as a PNG, SVG, or PDF, or rendered as an MP4 animation. Everything runs in the browser, with no installation required.

It may be handy for preparing a figure, illustrating a lecture, or simply playing with how a replication programme looks.

### Try it

- **Launch RepliCanvas:** [fberkemeier.github.io/RepliCanvas](https://fberkemeier.github.io/RepliCanvas/)
- **View the source on GitHub:** [github.com/fberkemeier/RepliCanvas](https://github.com/fberkemeier/RepliCanvas)

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid
            loading="eager"
            path="assets/img/blog/replicanvas-modes-demo.gif"
            alt="RepliCanvas showing linear, circular, and free-form DNA drawing modes"
            title="Linear, circular, and free-form DNA in RepliCanvas"
            class="img-fluid rounded z-depth-1"
            caption="Linear, circular, or entirely free-form—RepliCanvas turns DNA replication into a canvas."
        %}
    </div>
</div>
