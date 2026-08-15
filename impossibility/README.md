# impossibility/ — machine-verified results on where 94 cannot be

This directory contains **machine-checkable impossibility results**
(2026-08-15) that narrow the open question `N(18) = 93 or 94`. They do
not settle it. Every UNSAT verdict below carries a DRAT proof that was
verified with an independent checker; every SAT verdict was re-counted
by the package's own independent counters.

## Scope — read this first

All results are about **simple arrangements of pseudolines**
(every pair crosses, all crossing points distinct) that carry a given
symmetry. Pseudolines are strictly more general than straight lines,
so each UNSAT verdict covers straight lines too — that is a strength.
The limits:

* **Degenerate symmetric arrangements** (triple points / parallel
  pairs, which under a symmetry come in whole orbits) are **not**
  covered by the SAT model, with one exception: a triple point at a
  C3 center forces the count `≡ 0 (mod 3)`, hence `≤ 93` (arithmetic,
  see below). Against the remaining degenerate cases we only have
  strong stochastic evidence (500+ symmetric annealing runs of 10M
  orbit-moves with the degenerate frontier enabled: 94 never seen).
* Asymmetric arrangements are untouched. The gap `93 vs 94` stays open.

## Part 1 — arithmetic: most symmetries never had a chance

Let a rotation by `180°` act on lines: it maps every line to a
**parallel** line (itself only if the line passes through the center).
A triangle's symmetry group is `D3` (order 6): it has no element of
order 4, and a triangle fixed by a `180°` rotation would need two
parallel sides. Consequences for a symmetric arrangement of 18 lines:

| symmetry | triangle count is forced to | ceiling | verdict on 94 |
|---|---|---|---|
| C2 (180°) | even, but every off-center line pair becomes **parallel** | — | dead for simple |
| **C3 (120°)** | `≡ 0 or 1 (mod 3)` | 94 | **only compatible rotation** → tested below |
| C4 (90°) | `≡ 0 (mod 4)` | 92 | dead |
| C6 (60°) | `≡ 0 (mod 6)` | 90 | dead |
| C9 (40°) | `≡ 0 or 3 (mod 9)`, and `94 ≡ 4` | — | dead |
| C18 | contains C2 | — | dead |

Under C3, `94 = 3·31 + 1` forces **exactly one** rotation-invariant
triangle, whose three sides form one line-orbit and which contains the
center in its interior; a line through the center would give a central
triple point and cap the count at 93.

For a **reflection** acting on 18 simple pseudolines there are exactly
two families: fixed lines of a mirror are either the axis itself or
perpendicular to the axis, two perpendicular lines would be mutually
parallel (so at most one), and parity of the remaining paired lines
kills every other combination. The two families:

1. **axis-in-arrangement + one perpendicular line** (2 fixed lines);
2. **9 free mirror pairs, no fixed line** — here no triangle can be
   mirror-invariant, so the count is **even**: `94 = 47 pairs`.

Dihedral groups contain these as subgroups and inherit their ceilings.

## Part 2 — SAT: the compatible families, closed by certificates

CNF model: **Pavlo Savchuk's kobon-cnf** (arXiv:2507.07951,
<https://github.com/zegalur/kobon-cnf>), used as a generator without
modification, with its **native** `rotational_symmetry` and `mirrored`
options; our additions are purely additive clauses — a global
cardinality bound on missing segments (Sinz counter; `T ≥ 94 ⇔
missing ≤ 6`, `T ≥ 93 ⇔ missing ≤ 9`), and, for family (2), the
no-fixed-line mirror involution `A(r,i,k) ⇒ A(N−1−r, N−1−i, N−2−k)`,
derived empirically from 90 mirror-symmetric arrangements of real
lines and confirmed by the validation ladder below.

Solver: **Kissat 4.0.4** with DRAT proof output; every UNSAT proof
verified with **drat-trim**. Every SAT model extracted and re-counted
with the project's independent counter, and its symmetry re-checked.

| instance | family | verdict | kissat | proof |
|---|---|---|---|---|
| `rot3_k18_93` | C3 | **SAT** — a C3-symmetric 93 exists (see below) | 177 s | model re-counted: 93 |
| `rot3_k18_94` | C3 | **UNSAT** | 5 932 s | DRAT verified (2.1 GB) |
| `mir_k18_93` | mirror, 2 fixed lines | UNSAT | 23 s | DRAT verified |
| `mir_k18_94` | mirror, 2 fixed lines | UNSAT | 15 s | DRAT verified |
| `mir_k18_87…92` | mirror, 2 fixed lines | all UNSAT (family max < 87) | 8–31 s each | DRAT verified |
| `mir2_k18_94` | mirror, 9 free pairs | **UNSAT** | 3 498 s | DRAT verified (1.7 GB) |

**Headline results:**

1. *No simple C3-symmetric arrangement of 18 pseudolines forms 94
   triangles; the C3-symmetric simple maximum is exactly 93.*
2. *No simple mirror-symmetric arrangement of 18 pseudolines forms 94
   triangles* (both mirror families).
3. Combined with Part 1: **every nontrivial planar symmetry is either
   arithmetically excluded or certificate-closed — a simple
   94-triangle arrangement of 18 pseudolines, if it exists, is
   asymmetric.**

### Validation ladder (all green before touching k = 18)

* C3 model: k=6 SAT@7 / UNSAT@8 (the `mod 3` theorem reproduced
  inside the model, DRAT), k=9 SAT@21 (the known optimum), k=12
  UNSAT@38 (again the `mod 3` theorem) and SAT@37 — a **simple**
  C3-symmetric 37, which our annealing had only found with parallels.
* Mirror (2 fixed): k=5 SAT@5, k=7 SAT@11, each extraction
  symmetry-checked and re-counted.
* Mirror (9 pairs): k=8 SAT@14 — the known simple maximum of 8 lines,
  found inside the 4-pair family, involution verified on the model.

## The new arrangement: a C3-symmetric 93

`rot3_k18_93` produced a 93-triangle arrangement with combinatorial
120°-rotation symmetry — checked against all 2 337 arrangements of
this package by exact canonical form: **it is isomorphic to none of
them** (arrangement #2338, in effect). It was straightened to real
lines with exact rational certification in 0.3 s:

* `../c3_symmetric_93.svg` — figure (lines colored by σ-orbit)
* `../c3_symmetric_93_lines.json` — exact rational lines + floats
* `sat_rot3_k18_93_table.json` — the crossing-order table from SAT

## Reproduce it yourself

The CNF files (~140 MB each) and DRAT proofs (up to 2.1 GB) are too
large for this repository. Both are **deterministically reproducible**
from the scripts here; SHA-256 checksums of our originals are in
`SHA256_CNF_DRAT.txt`.

```bash
# regenerate an instance and solve it (kissat 4.0.4, drat-trim)
python3 -c "from kobon_sat_sym import run_step_sym; \
            run_step_sym('rot3_k18_94', 18, 6, 6, '?')"
# the script generates the CNF, runs kissat with proof logging,
# verifies UNSAT with drat-trim, or re-counts a SAT model.
```

Files: `kobon_sat_sym.py` (C3), `kobon_sat_mir.py` (mirror, 2 fixed),
`kobon_sat_mir2.py` (mirror, 9 pairs), `kobon_sat.py` (cardinality
machinery), `koboncnf_ext.py` (Savchuk's generator, unmodified model),
`kobon_comb.py` / `kobon_eventi.py` (independent counters), solver
logs (`*.log`) and structured results (`*.json`).

## Credit

The CNF model is **Pavlo Savchuk's kobon-cnf** — the same method that
settled `N(11) = 32` — including its built-in rotational and mirror
symmetry support. Our contribution here is the target-count
cardinality extension, the no-fixed-line mirror involution, the
validation ladders, and the k=18 runs with proof verification.
