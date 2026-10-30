# TopLoc: Efficient Conversational Search via Topical Locality in Dense Retrieval

This repository accompanies the SIGIR 2025 short paper
**"Efficient Conversational Search via Topical Locality in Dense Retrieval"**
(C. I. Muntean, F. M. Nardini, R. Perego, G. Rocchietti, C. Rulli — ISTI-CNR, Pisa).

[Paper (ACM DL)](https://doi.org/10.1145/3726302.3730186) · [arXiv:2504.21507](https://arxiv.org/abs/2504.21507)

## Overview

Response time is a key bottleneck of conversational search systems based on dense retrieval.
TopLoc exploits the *topical locality* of conversations, i.e., the tendency of the utterances of
a conversation to stay on related topics. The first utterance of a conversation is used to
identify the region of the embedding space that is relevant to the conversation; the following
utterances are answered by searching only that region.

We implement TopLoc on top of two families of approximate nearest-neighbour indexes in
[FAISS](https://github.com/facebookresearch/faiss):

| Variant | Index | Idea |
|---|---|---|
| TopLoc<sub>IVF</sub> | IVF | The first utterance selects a set of *h* centroids that is cached and reused for the whole conversation. |
| TopLoc<sub>IVF+</sub> | IVF | As TopLoc<sub>IVF</sub>, but the cached centroids are refreshed when their overlap with the centroids closest to the current utterance falls below a threshold α. |
| TopLoc<sub>HNSW</sub> | HNSW | The first utterance is answered with a larger candidate list; later utterances start the graph exploration from the point closest to the first utterance. |

On TREC CAsT 2019 and 2020, with Dragon and Snowflake Arctic Embed embeddings, TopLoc speeds up
retrieval by up to 10.4× with little loss in effectiveness, and by 4.4× without any loss
(see the paper for the full results).

## Experimental setting

- **Data:** TREC CAsT 2019 and 2020 (manually rewritten utterances; 38.6M passages).
- **Encoders:** [`facebook/dragon-plus-context-encoder`](https://huggingface.co/facebook/dragon-plus-context-encoder)
  (768-d, inner product; L2-normalised for HNSW) and
  [`Snowflake/snowflake-arctic-embed-l-v2.0`](https://huggingface.co/Snowflake/snowflake-arctic-embed-l-v2.0) (1024-d, cosine).
- **Metrics:** MRR@10, NDCG@3, NDCG@10 and average query response time.
- **Search:** FAISS low-level C++ API, single NUMA node (`numactl`), batched queries.

<!-- TODO(Guido): the repository currently contains no code, while the paper states that the code is
publicly available here. Push the cleaned scripts (embedding generation, IVF/HNSW index construction,
TopLoc search, evaluation) and then add an "Installation" and a "Reproducing the results" section
listing the exact commands. Remove this comment afterwards. -->

## Citation

```bibtex
@inproceedings{muntean2025toploc,
  author    = {Muntean, Cristina Ioana and Nardini, Franco Maria and Perego, Raffaele and Rocchietti, Guido and Rulli, Cosimo},
  title     = {Efficient Conversational Search via Topical Locality in Dense Retrieval},
  booktitle = {Proceedings of the 48th International ACM SIGIR Conference on Research and Development in Information Retrieval},
  series    = {SIGIR '25},
  pages     = {2749--2753},
  year      = {2025},
  publisher = {ACM},
  doi       = {10.1145/3726302.3730186}
}
```

## License

See [LICENSE](LICENSE).
