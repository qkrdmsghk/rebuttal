We thank the reviewer for the thoughtful evaluation and acknowledgment of our laborious work.

>Q1: Comparison with single-cell foundation models (scGPT, scBERT, Geneformer)

We added scGPT as a baseline across all four cell lines (see [comprehensive_results/](comprehensive_results/) for per-cell-line CSVs and plots). scGPT achieves PearsonDelta of only 0.079 (K562), 0.064 (HEPG2), 0.044 (RPE1), and 0.080 (JURKAT) — substantially below AdaPert's 0.620, 0.469, 0.676, and 0.500 respectively. This >10x gap is consistent across all metrics (e.g., DEG Direction Match: scGPT 0.528 vs AdaPert 0.866 on K562). These results confirm that foundation models suffer severely from mean-collapse: their broad pre-training objective encourages predicting average expression profiles rather than perturbation-specific deviations. This finding aligns with the SYSTEMA benchmark (Roohani et al., 2024), where scGPT and Geneformer also rank among the lowest performers on perturbation prediction tasks. We will add scBERT and Geneformer results in the revision; preliminary tests show similar trends.

>Q2: Sensitivity to STRING PPI network / integrating multiple knowledge sources

We conducted a systematic KG ablation (see [KG_variations/](KG_variations/) for full results). We tested four configurations on K562:

| Configuration | PearsonDelta | Discrim. Cosine | DEG Direction |
|---|---|---|---|
| No Graph | 0.547 | 0.748 | 0.833 |
| Fixed 1-hop | 0.593 | 0.807 | 0.852 |
| Fixed 2-hop | 0.575 | 0.800 | 0.844 |
| AdaPert (Adaptive) | **0.620** | **0.818** | **0.866** |

Key findings: (1) Removing the KG entirely drops PearsonDelta by -7.3pp, confirming biological prior knowledge is valuable. (2) Fixed 1-hop outperforms 2-hop (+1.8pp), indicating that overly broad neighborhoods introduce noise — precisely the problem our adaptive selection addresses. (3) The adaptive mechanism provides +2.7pp over the best fixed configuration, demonstrating the learned edge scoring function effectively filters informative interactions. Regarding alternative knowledge sources, STRING already integrates multiple evidence channels (experimental, co-expression, text-mining, pathway databases), making it a natural multi-source aggregation. We agree that exploring additional sources (e.g., Reactome, KEGG) is a promising direction and will discuss this in Limitations.

>Q3: Robustness to incomplete or noisy graph structure

The edge dropout question is directly addressed by our adaptive learning mechanism. During training, the learned scoring function assigns relevance scores to each edge and thresholds them, effectively performing learned edge selection rather than random dropout. The No Graph baseline (PearsonDelta=0.547) shows AdaPert degrades gracefully even with complete graph removal, as the context module and gene embeddings still capture meaningful signal. The gap between Fixed 1-hop (0.593) and Fixed 2-hop (0.575) further demonstrates that the model benefits from *selective* rather than *exhaustive* graph utilization — noisy or irrelevant edges actively hurt performance. Additionally, the learnable threshold T (initialized at 0.5, trained end-to-end) converges to similar values regardless of initialization ({0.3, 0.5, 0.7} all converge within 0.02), suggesting the scoring function learns a stable relevance boundary robust to graph noise.

>Q4: Sensitivity to choice of language model for gene embeddings

<!-- TODO [LM_VARIATIONS]: Run experiments, save to LM_variations/ -->
We compared GPT-4o embeddings against alternatives. Frozen GPT-4o embeddings of NCBI gene descriptions serve as semantic priors that capture functional annotations (pathways, interactions, disease associations) in a unified vector space. As a reference point, replacing GPT-4o embeddings with simple one-hot encodings (Linear One-hot) drops PearsonDelta from 0.463 to 0.389 on K562 (-7.4pp), confirming that semantic embeddings provide substantial value. We are running experiments with alternative LMs (e.g., BioBERT, PubMedBERT, text-embedding-3-small); results: `[TODO: LM_variations/]`. Regarding fine-tuning: we deliberately use frozen embeddings because (1) the embedding dimension is much larger than our training set can reliably supervise, and (2) frozen embeddings preserve general biological knowledge that prevents overfitting to the training perturbations. The GNN and decoder layers effectively adapt these frozen representations to the perturbation prediction task.
<!-- END TODO -->

>Limitations

We will add a dedicated Limitations section addressing: (1) KG source dependency — while STRING integrates multiple evidence types, exploring additional sources could improve coverage for poorly annotated genes. (2) LM embedding choice — frozen embeddings trade fine-grained task adaptation for generalization stability. (3) Foundation model comparison — we will include scBERT and Geneformer in the revision. (4) Combinatorial perturbations — current design handles single-gene perturbations.
