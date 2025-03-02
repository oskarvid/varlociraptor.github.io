+++
title = "Filtering variant calls"
weight = 3
+++

Varlociraptor provides built-in filtering mechanisms. Most importantly, these are designed to only require a single, meaningful parameter.

## Filtering by controlling FDR

Variant calls from varlociraptor can be filtered by controlling the false discovery rate (FDR). You can choose to either control the global or the local FDR.
We recommend the local FDR for the following reason.

Controlling the global FDR means that Varlociraptor will try to find a set of variants that most closely reaches the given FDR threshold.
Hence, it will start with the very clear cases, followed by borderline cases, and if there is still something left up to the desired threshold, it will even add clearly wrong variant calls, just to guarantee that the FDR is as close as possible to the desired threshold.

In contrast, local FDR control means that for each individual variant call, the probability to be a false discovery may not exceed the given threshold.
This leads to more intuitive results, since no obvious false positives are inserted just to reach the desired threshold as it happens with global FDR control.

### Control local FDR

Let `calls.bcf` be the output of the varlociraptor calling command.
Then, by running e.g.

```bash
varlociraptor filter-calls control-fdr --local calls.bcf --events SOMATIC_TUMOR --fdr 0.05
```

calls can be filtered to all single nucleotide variants (SNVs) that newly occur in a tumor sample (i.e. are somatic) with a **local** FDR of 5%.

### Control global FDR

For controlling the global FDR, you could issue the following command:

```bash
varlociraptor filter-calls control-fdr calls.bcf --events SOMATIC_TUMOR --fdr 0.05 --var SNV
```

The reason for stratifying by variant type here is that their probabilities can fall into largely different ranges.
For example, most likely the probabilities for SNVs are likely much stronger than for indels and structural variants.
Hence, performing a combined instead of stratified FDR control would lead to a domination of SNVs in the resulting set.
Instead, by stratification we get the maximum resultion for each type of variant in case of global FDR control.