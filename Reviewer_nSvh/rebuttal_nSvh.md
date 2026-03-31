We thank the reviewer for the thorough evaluation and recognition of our work as "well written" and "technically sound."

[W1&W9] KG Dependency + Fixed Subgraph Baseline

refer TBD (may 1-2 sentences)

[W2&W3] DEG Selection Clarification + Sensitivity

refer TBD (may 1-2 sentences)

[W4&W5&W7&Q1] Simpler Baselines + Multi-Dataset Validation + unseen cel line generalization
We thank the reviewer for this suggestion. 这让我们的实验更加的comprehensive
>W4: Simpler Baselines
> The comparison with other methods is appreciated, but comparison with simpler linear models or mean baseline (as in the STATE paper https://doi.org/10.1101/2025.06.26.661135) also needs to be included. Based on such results, the authors should critically assess to what degree the added computational complexity provides substantial performance gains over simple baselines.
> （根据/home/yinhua/CellPertQA/rebuttal/Reviewer_nSvh/comprehensive_results 这一段主要讲 simpler linear models or mean baseline的性能情况，说明他们的特点。（在K562，简单的baseline表现的还是不错的，不过在别的数据集上就比较borderline。）我们模型对比linear genept 多的内容是 adaptive sub knowledge graph（这里可能多一些marginal的内存）+DEG guidance（无内存负担）的情况下，在所有的数据集上表现最优。然后 500 characters）
[W4] We have expanded our baselines to include the mean baselines (perturbation/context-aware mean) referenced in the STATE paper, as well as linear models with one-hot encoding and semantic GenePT embeddings (Ji et al., Nature Methods 2025). Results across all four cell lines (K562, RPE1, JurKat and HEPG2) reveal two key findings. (1) Simple baselines are competitive but inconsistent. On K562, Linear-GenePT achieves a respectable Pearson Delta of 0.463, yet on HepG2 (0.400) and Jurkat (0.420), its performance gain is marginal. No simple baseline consistently ranks second across all settings. (2) AdaPert's gains are consistent and significant. AdaPert outperforms the best linear baseline (Linear-GenePT) by +6.7% to +33.9% in Pearson Delta across all four cell lines (K562: 0.620 vs 0.463; HepG2: 0.469 vs 0.400; Jurkat: 0.500 vs 0.420; RPE1: 0.676 vs 0.633), with statistical significance confirmed across all metrics. Regarding computational overhead: relative to linear baselines (0.5M), AdaPert (8.3M) adds only an adaptive sub-KG extraction module (modest memory cost scaling with local neighborhood size) and a DEG-guidance loss (no additional memory), and provides consistent performance gains over simple baselines across the datasets.

> W5: The reliance on a single dataset for validation (two cell lines from the same paper, to be precise) is a major weakness and leaves it entirely open how well the method may generalize.
> W7: The authors evaluate their model on unseen perturbations, but it would also be valuable to assess how well the model generalizes to unseen cell lines, and potentially across different cell types and donors.
> Q1: Based on the “Data Statistics” section, the authors state that the evaluation is performed on the K562.Replogle and RPE1.Replogle datasets (line 564), while the main text also mentions HEPG2.Replogle (line 297). Could the authors clarify which datasets were used? Would it be possible to include additional dataset for validation to show AdaPert’s performance on unseen cell lines?
> (/home/yinhua/CellPertQA/rebuttal/Reviewer_nSvh/comprehensive_results 参考)

[W5, W7, Q1] We apologize for the dataset confusion and have substantially expanded evaluation: (1) Four cell lines, unseen perturbations (Figures Xa–Xd). We added HEPG2 (liver cancer) and JURKAT (T-cell leukemia) to the existing K562 and RPE1. AdaPert ranks first across all four datasets on most metrics. (2) Under unseen cell line setting, Perturb Mean achieves the
strongest performance across all metrics, benefiting from its mean-aware construction rather than perturbation-conditioned prediction. Among learned methods, AdaPert and Linear show competitive performance, both substantially outperforming TxPert and foundation models. These results suggest that perturbation-conditioned learning generalizes well to unseen genetic contexts, but cross-cell-line transfer may benefit from larger model capacity. (3) Systema benchmark (Figures Xf–Xj). All methods evaluated under the Systema protocol across all five settings. AdaPert consistently achieves the best Pearson Delta and Centroid Accuracy. See figure captions for per-setting details.

> W6, W10, W11, Q2, Q3: Mean-Collapse, DEG/Non-DEG Metrics, and Ablation

These five points center on how mean-collapse manifests and how AdaPert addresses it. We address them jointly.
>Q3: Supporting the Mean-Collapse Hypothesis
> The authors argue that mean-collapse arises from a mismatch with the sparse nature of perturbation responses, but don't explore this hypothesis.
We thank the reviewer for encouraging deeper investigation. Three lines of evidence collectively support this claim: (1) rank-weighted DEG metrics confirm gains concentrate on perturbation-affected genes (W6, Figure Xa.(Reviewer_nSvh/mean_collapse/260331_deg_nondeg_comparison_final.png)); (2) non-DEG metrics show no degradation, with proportionally smaller improvement (W10, Figure Xa(Reviewer_nSvh/mean_collapse/260331_deg_nondeg_comparison_final.png)); (3) stratified ablation reveals that context extraction has the largest impact where sparsity is most severe (Q2, Figure Xb(Reviewer_nSvh/mean_collapse/ablation_barplot_nature.png)). 

>W6 & W10: Weighted MSE on DEGs and Non-DEG Metrics
> W6: Weighted MSE with higher weight on DEG genes would be more appropriate.
> W10: Report metrics for non-DEG genes to support the claim.

Following the reviewer's suggestion, we computed rank-weighted MSE (wMSE, weights inversely proportional to DEG rank by |LFC|) and unweighted MSE, separately for DEG and non-DEG genes, across Linear+GenePT (Ji et al., 2025), TxPert, and AdaPert (Figure Xa). On wMSE, AdaPert's improvement over TxPert is larger on DEGs (3.0%) than non-DEGs (1.8%), confirming gains target the highest-impact genes most susceptible to mean-collapse. The consistent progression Linear → TxPert → AdaPert across all panels demonstrates progressive mitigation. See Figure Xa caption for full details.

>W11: Mean-Collapse Comparison Scope
> Why is mean-collapse compared only between AdaPert and TxPert?

TxPert shares AdaPert's core architecture (autoencoder + KG embeddings), making it the most controlled comparison. We now include Linear+GenePT as an additional baseline in Figure Xa, showing that mean-collapse (highest wMSE_DEG) is most severe in simpler models and progressively mitigated by KG integration and adaptive context extraction.

>Q2: Ablation Effect Size
> Removing z-context and non-DEG loss has a moderate effect. Perhaps reporting median values would help.

We report both mean and median stratified by perturbation strength (Figure Xb). The context module's effect becomes critical when stratified: on weak perturbations (<5% DEGs, n=92), removing context degrades median Pearson Delta by −6.7%; on strong perturbations (>10% DEGs, n=100), context removal degrades median Discrimination L1 by −12.3% and L2 by −13.3%. This confirms that adaptive components specifically address sparsity-induced collapse rather than providing uniform gains. See Figure Xb caption for full stratified metrics.



> Q4: Downstream Utility
> The authors do not demonstrate whether AdaPert would substantially improve downstream analyses, such as target selection, compared to simpler baselines.
参考这个：/home/yinhua/CellPertQA/rebuttal/Reviewer_nSvh/downs_stream_analysis 

We thank the reviewer for raising this point. We evaluated downstream utility through three analyses on all 272 K562 test perturbations, comparing AdaPert against TxPert (GATv2 baseline) and a Linear model (https://qkrdmsghk.github.io/rebuttal/#nSvh_downstream).

**DEG identification (Table X).** AdaPert significantly improves DEG recovery over TxPert (Overlap@100: 0.258 vs. 0.235, p = 3.6×10⁻⁸) and regulation direction prediction (0.866 vs. 0.850, p = 7.3×10⁻³). See Table X caption for full metrics.

**Pathway recovery (Figure Xa).** GSEA on predicted profiles shows AdaPert recovers 20.0% of ground-truth significant pathways, compared to 17.9% (TxPert) and 11.4% (Linear), with the highest direction accuracy (0.573). Details in Figure Xa caption.

**Target prioritization (Figure Xb).** We ranked perturbations by predicted pathway effect and compared against ground-truth rankings. AdaPert achieves mean Spearman ρ = 0.207 across 44 Hallmark pathways, with clear advantages on biologically relevant pathways including Glycolysis and mTORC1 Signaling (Figure Xb).

These results confirm that AdaPert's metric improvements translate into practical gains for DEG identification, pathway analysis, and perturbation-based target selection.


