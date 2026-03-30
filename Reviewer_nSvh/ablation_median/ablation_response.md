# Rebuttal: Ablation Study (Reviewer Comment on Figure 4)

## Reviewer Comment

> It seems that removing z-context and non-DEG loss has overall a rather moderate effect on model performance. Perhaps reporting median values would be more helpful to assess the effect size of the changes (Figure 4). It may be worth highlighting the model's capacity to better discriminate small perturbations (Figure 4, top right), with overall best performance of the model for large perturbations.

## Response

We thank the reviewer for this constructive suggestion. Indeed, jointly reporting mean and median values reveals clearer effect sizes than mean alone, as median is more robust to outlier perturbations and better reflects typical model behavior. Following this suggestion, we now report both statistics stratified by perturbation size (percentage of differentially expressed genes: <5%, 5-10%, >10%).

### Comprehensive evaluation of perturbation discrimination

To provide a more comprehensive evaluation of the model's ability to discriminate between different perturbations, we employ three complementary discrimination metrics based on distinct distance measures, each capturing different aspects of prediction quality:

- **L1 (Manhattan) distance** treats all gene-level prediction errors equally, measuring overall magnitude fidelity across all genes.
- **L2 (Euclidean) distance** assigns greater weight to large deviations due to the squared term, making it particularly sensitive to accuracy on highly differentially expressed genes.
- **Cosine similarity** evaluates the directional consistency of the predicted expression profile, i.e., whether the model correctly identifies which genes are up- or down-regulated, independent of the magnitude of changes.

Together, these three metrics assess both the directional accuracy (cosine) and magnitude fidelity (L1/L2) of the predictions, providing a complete picture of model performance.

### Stratified analysis reveals scale-specific contributions

The stratified analysis reveals that the contributions of each component are concentrated at different perturbation scales, and the moderate overall effect results from averaging across a medium-sized group (5-10% DEGs) where both components provide limited marginal benefit.

**Small perturbations (<5% DEGs, n=92):** The non-DEG loss provides the largest improvement, most prominently in directional metrics:

- Pearson delta: +6.7% (median), +4.8% (mean)
- Discrimination cosine: +6.7% (median), +3.8% (mean)
- Discrimination L1/L2: comparable across variants

For perturbations affecting fewer than 5% of genes, the true expression signal is subtle and sparse. Without the non-DEG loss, the model tends to predict small spurious changes across the remaining 95%+ non-perturbed genes, producing false positive signals that are proportionally large relative to the weak true signal. These false positives corrupt the directional profile of the prediction, which is why cosine similarity — the metric most sensitive to directional accuracy — shows the largest improvement. The non-DEG loss explicitly suppresses these spurious predictions, preserving the sparsity of the perturbation signature and improving the signal-to-noise ratio. L1 and L2 metrics show smaller gains because they are dominated by noise when the absolute magnitude of true changes is small.

**Large perturbations (>10% DEGs, n=100):** The context module provides the largest improvement, most prominently in magnitude-sensitive metrics:

- Discrimination L2: +13.3% (median), +7.2% (mean)
- Discrimination L1: +12.3% (median), +6.5% (mean)
- Pearson delta: +4.9% (mean), +4.6% (median)
- Discrimination cosine: near saturation (>0.98 for all variants)

Large perturbations involve complex cascading effects across the gene regulatory network, affecting hundreds of genes with varying magnitudes. The cosine metric is near saturation for all model variants because all models correctly identify the general direction of changes when the perturbation signal is strong. The critical challenge for large perturbations is predicting the correct magnitude of change for each gene — this is precisely what L1 and L2 metrics capture, and where the context module provides its largest benefit. By aggregating information from related perturbations, the context module enables the model to learn perturbation-specific downstream effect patterns, improving the quantitative accuracy of predictions for complex perturbations.

**Medium perturbations (5-10% DEGs, n=80):** Both components contribute minimally (differences <1.5% across all metrics). The perturbation signal is strong enough for the base GNN architecture to capture via message passing on the PPI network, yet not so complex as to require additional context aggregation. This group dilutes the concentrated improvements at the extremes when results are reported in aggregate.

### Summary

| Component | Primary contribution | Mechanism | Key metric | Evidence |
|-----------|---------------------|-----------|------------|----------|
| Non-DEG loss | Small perturbations (<5%) | Suppresses false positives in non-DEG genes → improves signal-to-noise ratio | Cosine (directional) | +6.7% median |
| Context module | Large perturbations (>10%) | Aggregates related perturbation information → improves magnitude accuracy | L1/L2 (magnitude) | +12.3% / +13.3% median |

The two components serve complementary roles at different perturbation scales: the non-DEG loss improves directional accuracy for subtle perturbations by suppressing false positives, while the context module improves magnitude fidelity for complex perturbations by leveraging cross-perturbation information. The apparently moderate overall effect reflects the averaging of these concentrated, scale-specific contributions with a medium group where the base architecture is already sufficient.

We have updated Figure 4 to report both mean and median values stratified by DEG percentage, using all three discrimination metrics for comprehensive evaluation.
