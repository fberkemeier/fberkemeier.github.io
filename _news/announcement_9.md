---
layout: post
title: "Can a genome language model learn where DNA replication begins?"
date: 2026-01-30 10:00:00-0400
inline: false
related_posts: false
---

For decades, one of the most persistent puzzles in human DNA replication has been deceptively simple to state: where does replication actually begin?

In human cells, replication initiates at tens of thousands of sites, yet no single DNA motif defines these origins. Instead, origin usage has been associated with broad features such as GC content, G-quadruplex motifs, and chromatin state, none of which are sufficient on their own. In this work, we asked whether a genome language model could learn the extended sequence context that enables a genomic region to initiate replication.

We developed [ORILINX](https://github.com/Pfuderer/ORILINX), a genome language model fine-tuned on experimentally mapped human replication origins, which predicts origin locations directly from primary DNA sequence (Pfuderer *et al.*, 2026). The model captures information beyond simple sequence features, accurately identifying both strong and weaker replication origins across the human genome.

When applied genome-wide, ORILINX predictions closely align with independent estimates of origin efficiency inferred from replication timing and mathematical modelling. Regions predicted to be rich in competent origins also tend to contain many efficient firing sites, despite the model having no access to replication timing data.

Notably, ORILINX generalises across species. Although trained only on human data, it accurately predicts replication origins in mouse, sheep, and chicken genomes, suggesting that conserved sequence principles underlie replication initiation across vertebrates.

### Resources

- **Read our preprint:** [https://www.biorxiv.org/content/10.64898/2026.01.29.702604v1](https://www.biorxiv.org/content/10.64898/2026.01.29.702604v1)
- **Try ORILINX:** [https://github.com/Pfuderer/ORILINX](https://github.com/Pfuderer/ORILINX)

Together, this work shows that DNA sequence does encode meaningful information about where replication can begin, and that genome language models provide a scalable way to extract this information directly from sequence alone.

