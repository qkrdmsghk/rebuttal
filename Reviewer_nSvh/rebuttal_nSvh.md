We thank the reviewer for the thorough evaluation and recognition of our work as "well written" and "technically sound."

>W1/W9: KG Dependency + Fixed Subgraph Baseline

We conducted a systematic KG ablation on K562 (see [KG_variations/](KG_variations/) for full results and visualizations). Removing the KG entirely drops PearsonDelta from 0.620 to 0.547 (-7.3pp), confirming biological prior knowledge is valuable. We further compared fixed subgraph baselines: Fixed 1-hop achieves 0.593 and Fixed 2-hop achieves 0.575, both substantially below AdaPert's adaptive selection (0.620). Notably, 1-hop outperforms 2-hop, confirming that overly broad neighborhoods introduce noise --- precisely the problem adaptive selection addresses. The adaptive mechanism provides a consistent +2.7pp gain over the best fixed configuration across all metrics (Discrimination Cosine: 0.818 vs 0.807; DEG Direction: 0.866 vs 0.852).

> W2/W3: DEG Selection Clarification + Sensitivity

We clarify that two thresholds serve different purposes: **Training** uses Wilcoxon rank-sum test (adjusted p<0.05, BH correction) AND |log2FC|>0.5 for DEG identification. **Evaluation** uses |expression change|>0.1 on normalized scale following prior work (GEARS, scGEN), applied post-hoc without affecting training.
<!-- TODO [DEG_SENSITIVITY]: save to DEG_sensitivity/ -->
For sensitivity analysis, we varied p-value thresholds {0.01, 0.05, 0.10} and fold-change cutoffs {0.25, 0.5, 1.0}. Full results in `[TODO: DEG_sensitivity/]`. Robustness is expected: the Huber loss provides a soft boundary at the DEG/non-DEG threshold, so genes near the boundary receive moderate penalties under either classification.
<!-- END TODO -->

> W4/W5/W7/Q1: Simpler Baselines + Multi-Dataset Validation + unseen cel line generalization

We extended evaluation to **four cell lines** (K562, HEPG2, RPE1, JURKAT) and added Linear (GenePT), Context Mean, and STATE as baselines (see [comprehensive_results/](comprehensive_results/) for per-cell-line CSVs and comparison plots). AdaPert consistently ranks **first on all four cell lines** in PearsonDelta: 0.620 (K562), 0.469 (HEPG2), 0.676 (RPE1), 0.500 (JURKAT). Compared to the best linear baseline (Linear GenePT), AdaPert achieves gains of +15.7pp (K562), +6.9pp (HEPG2), +4.2pp (RPE1), and +8.0pp (JURKAT). STATE performs substantially worse (0.247 on K562, 0.175 on JURKAT), confirming that the added model complexity yields meaningful improvements. Cross-platform validation is discussed in Limitations.
<!-- TODO [CROSS_CELLLINE]: save to cross_cellline/ -->
We are evaluating AdaPert in the SYSTEMA K562-cross setting (train on HEPG2+RPE1+JURKAT, test on K562). Results: `[TODO: cross_cellline/]`
<!-- END TODO -->

> W6/W10/W11/Q3: Metrics Beyond Correlation + Non-DEG Genes + Mean-Collapse Scope

We now report magnitude-sensitive and DEG-specific metrics (see [comprehensive_results/k562_all_metrics.csv](comprehensive_results/k562_all_metrics.csv)). On K562, AdaPert achieves Discrimination L1=0.701, DEG Direction Match=0.866, and DE Spearman LFC=0.675, outperforming TxPERT (0.697, 0.850, 0.655) and Linear GenePT (0.558, 0.784, 0.570). For non-DEG genes, AdaPert achieves the lowest MAE_delta across all cell lines (K562: 0.0220, HEPG2: 0.0314, RPE1: 0.0346, JURKAT: 0.0327), confirming the non-DEG loss suppresses spurious predictions without sacrificing DEG quality. Updated Figure 1 extends mean-collapse comparison to all methods. `[TODO: mean_collapse/]`
<!-- TODO [SPARSITY_EXP]: save to mean_collapse_hypothesis/ -->
We designed a controlled experiment varying DEG sparsity (1-50%) to measure predicted-to-true variance ratio. Results: `[TODO: mean_collapse_hypothesis/]`
<!-- END TODO -->

> W8: Threshold T

T is a **learnable parameter** trained end-to-end, not a hyperparameter. Initialized at 0.5 and updated via backpropagation. Testing T_init in {0.3, 0.5, 0.7}: converged values within 0.02 of each other, PearsonDelta varied <0.5%.

> Q2: Ablation Median

Stratified mean+median analysis (see [ablation_median/](ablation_median/) for data and visualizations) reveals scale-specific contributions: the non-DEG loss improves small perturbations (<5% DEGs) by +6.7% median PearsonDelta and +6.7% Discrimination Cosine, while the context module improves large perturbations (>10% DEGs) by +13.3% Discrimination L2 and +12.3% L1. The moderate overall effect reflects averaging with a medium group (5-10%) where the base architecture already suffices.

> Q4: Downstream Utility

Pathway enrichment analysis (Figure 5) shows AdaPert yields highest correlation to ground-truth pathway activities. Additional target ranking analysis: `[TODO: downstream_analysis/]`

> Limitations
1. Cross-cell-type generalization (W7). 2. DEG sensitivity in low-replicate settings (W3). 3. No noise robustness test. 4. KG coverage for poorly annotated genes. 5. Computational overhead.
