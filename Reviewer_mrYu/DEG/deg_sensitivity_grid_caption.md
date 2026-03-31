# Figure: DEG Definition Sensitivity Analysis

**Sensitivity of AdaPert to DEG definition thresholds across three cell lines (K562, JURKAT, RPE1).** Each row corresponds to a dataset. The first column (orange) shows the average number of DEGs per perturbation under each threshold combination. The remaining six columns (blue) show prediction performance across six evaluation metrics: Pearson Delta, MAE Delta, Discrimination L1, Overlap@50, DE Spearman LFC, and DE Direction Match. The x-axis varies FDR threshold (0.05 vs 0.01) and the y-axis varies log-fold-change cutoff (none, |LFC|>0.25, |LFC|>0.50). Each heatmap is independently normalized to highlight within-metric variation.

Stricter thresholds substantially reduce the number of DEGs used during training (e.g., K562: 641→229, JURKAT: 222→63, RPE1: 505→228) but have minimal impact on prediction performance across all six metrics and all three datasets. For instance, Pearson Delta varies by less than 1.3% in K562 (0.612–0.620), 2.1% in JURKAT (0.486–0.507), and 0.7% in RPE1 (0.669–0.674).

**Key insights:**

1. **Moderate thresholds perform best.** Across datasets, FDR<0.05 with |LFC|>0.25 consistently yields the best or near-best performance (e.g., K562 Pearson Delta 0.620, JURKAT 0.506), suggesting that a moderate filter balances signal quality with sufficient DEG coverage for the non-DEG L1 regularization to be effective.

2. **Overly strict thresholds slightly hurt.** The most restrictive setting (FDR<0.01, |LFC|>0.50) tends to underperform slightly (e.g., K562 Pearson Delta 0.612 vs 0.620), likely because too few genes are labeled as DEGs, reducing the contrast between DEG and non-DEG loss terms and weakening the regularization signal.

3. **FDR matters more than LFC.** Within the same LFC cutoff, switching FDR from 0.05 to 0.01 often has a larger effect than increasing LFC (e.g., JURKAT no-LFC: 0.507→0.486 across FDR, vs 0.507→0.501 across LFC), indicating that the model is more sensitive to the statistical significance boundary than the effect size cutoff.

4. **Dataset-dependent patterns.** RPE1 shows the least sensitivity (all metrics within <1%), while JURKAT shows the most variation, correlating with JURKAT having the fewest DEGs per perturbation (63–222 vs K562: 229–641). This suggests that DEG threshold sensitivity increases when DEG counts are low.

These results demonstrate that AdaPert is robust to the specific DEG definition, and practitioners can use standard thresholds (FDR<0.05, |LFC|>0.25) without extensive tuning.
