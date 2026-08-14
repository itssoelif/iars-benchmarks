# IARS — Benchmark Instances and Algorithm Pseudocode

Companion repository for the PhD thesis

> **Uygunluk ve Kapasite Kısıtlı İlişkisiz Paralel Makine Çizelgeleme Problemi için
> Zeki ve Hibrit Bir Model Önerisi**
>
> Elif Yıldırım — T.C. Sakarya Üniversitesi, Fen Bilimleri Enstitüsü
> Tez danışmanı / Supervisor: Doç. Dr. Berrin Denizhan

This repository hosts two artefacts referenced by the thesis:

1. **[`PSEUDOCODE.md`](PSEUDOCODE.md)** — the IARS (irace–ADAM–SA) pseudocode,
   reproduced from the thesis (Algoritma 1, §3.3.4). Turkish, as in the source; the
   body is verbatim and only the algorithm title was shortened.
2. **[`instances/taillard_pfsp/`](instances/taillard_pfsp/)** — the 120 literature
   Taillard permutation flow-shop benchmark instances (`ta001`–`ta120`) used in the
   external-validity study, each verified bit-for-bit against Taillard's published seed.

## Scope — read this before using the instances

The instances in this repository are **flow-shop (PFSP)** instances. They are *not*
the thesis's primary benchmark archive.

| | Problem | Where it appears in the thesis | In this repo |
|---|---|---|---|
| Primary benchmark set | $R_m \mid M_p, \mathrm{cap}_m \mid C_{\max}$ (unrelated parallel machines, eligibility + capacity) | Chapter 5 comparison (Gupta-mapped instances) | ✗ |
| Industrial data | Banbury mixer scheduling, real production data | Chapters 3–4 | ✗ (confidential) |
| **Taillard PFSP** | $F_m \mid prmu \mid C_{\max}$ | External-validity panel (IARS-FS) | ✓ |

The industrial and Gupta-mapped instances are derived from real tire-plant production
data and are not redistributed here.

## Taillard instances

### Provenance

The files were obtained from the [`chneau/go-taillard`](https://github.com/chneau/go-taillard)
mirror of Taillard's benchmark set and then **regenerated from each file's own published
seed** with Taillard's portable pseudo-random generator (Park–Miller / MINSTD `unif`,
$a = 16807$, $m = 2^{31} - 1$). All 120 files reproduce bit-for-bit:

```
--- 120/120 instances reproduce from their own seed
```

This check guards against a corrupted or substituted mirror. It is re-runnable from the
thesis code base (`benchmarking/taillard_pfsp.py`, `verify_against_generator`).

These instances are **Taillard's**, not a contribution of this thesis. Cite the original:

> Taillard, E. (1993). Benchmarks for basic scheduling problems.
> *European Journal of Operational Research*, 64(2), 278–285.
> <https://doi.org/10.1016/0377-2217(93)90182-M>

### File naming

`tai{n}_{m}_{k}.fsp` — `n` jobs, `m` machines, `k ∈ {0..9}` the index within the size
group. Twelve size groups × 10 instances = 120 files:

```
(20,5) (20,10) (20,20) (50,5) (50,10) (50,20)
(100,5) (100,10) (100,20) (200,10) (200,20) (500,20)
```

Taillard's canonical `ta001`–`ta120` numbering walks these groups in the order above,
ten instances each — so `tai20_5_0` is `ta001` and `tai500_20_9` is `ta120`.

### File format

```
number of jobs, number of machines, initial seed, upper bound and lower bound :
         100          10  1539989115        5770        5759
processing times :
 52 95 42 75 44 ...        <- machine 1, one column per job
 82 21 79 95 46 ...        <- machine 2
 ...
```

Line 2 carries `n`, `m`, the generator seed, and Taillard's published upper and lower
bounds. The processing-time block is **machine-major**: row $i$ holds the processing
times of all $n$ jobs on machine $i$.

### Minimal reader

```python
from pathlib import Path
import numpy as np

def read_fsp(path: Path):
    """Read a Taillard .fsp file. Returns (n, m, seed, ub, lb, processing[m, n])."""
    tokens = path.read_text().split("\n")
    n, m, seed, ub, lb = (int(v) for v in tokens[1].split())
    rows = [[int(v) for v in tokens[3 + i].split()] for i in range(m)]
    return n, m, seed, ub, lb, np.array(rows, dtype=int)
```

Makespan under a job permutation $\pi$ is the usual max-plus recursion
$C_{i,j} = \max(C_{i-1,j},\, C_{i,j-1}) + p_{i,\pi(j)}$, with $C_{\max} = C_{m,n}$.

## Licence

See [`LICENSE`](LICENSE). Repository-authored content (README, pseudocode) is
CC BY 4.0; the Taillard instance files are third-party benchmark data redistributed
under their original terms of free academic availability.

## Citing

See [`CITATION.cff`](CITATION.cff).
