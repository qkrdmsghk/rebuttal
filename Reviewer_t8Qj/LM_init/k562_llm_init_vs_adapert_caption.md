# Figure: LLM-Init Node vs AdaPert (K562)

## Architecture Difference

```
AdaPert (default):
  Node embedding = Learned(nn.Embedding) + Linear(LLM_emb)
  → Two-channel: task-specific learned features + LLM semantic prior
  → Both updated during training

LLM-Init Node:
  Node embedding = nn.Embedding initialized from Linear(LLM_emb)
  → Single-channel: LLM-projected initialization, then trained freely
  → LLM projection weights are NOT updated (used only for init)
  → 9854/18216 (54%) genes initialized; rest remain random
```

## Caption

**Comparison of node embedding strategies on K562 single-cell perturbation prediction.** AdaPert uses a dual-channel node embedding where learned embeddings (`nn.Embedding`) are additively combined with a linear projection of GenePertESM LLM embeddings (`Linear(3072→128)`) at every forward pass. LLM-Init Node instead initializes `nn.Embedding` weights from projected LLM embeddings and discards the LLM projection afterward, training the embeddings freely. AdaPert outperforms LLM-Init Node across all six metrics: Pearson Delta (+4.6%), MAE Delta (-2.5%), Discrimination L1 (+5.2%), Overlap@50 (+5.7%), DE Direction Match (+1.2%), and DE Spearman LFC (+2.3%). Note that LLM-Init Node was trained for only 85/200 epochs (crashed), which may partially explain the gap. Potential reasons for the performance difference: (1) only 54% of genes (9854/18216) have LLM embeddings, leaving the rest with random initialization and creating an inconsistent embedding space; (2) the linear projection used for initialization is itself randomly initialized, so the projected LLM values may not be more informative than random embeddings at epoch 0; (3) AdaPert's additive fusion preserves continuous access to LLM semantic information throughout training, while LLM-Init loses this signal after initialization as the embeddings drift during optimization.
