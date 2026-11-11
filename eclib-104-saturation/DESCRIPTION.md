# Restore the auxiliary-prime window in p-saturation (#104)

On [31530h2 in issue #104](https://github.com/JohnCremona/eclib/issues/104), the default saturation search returns unknown for `(132,648)` on `[1,0,0,71050,-11173128]`. The first auxiliary prime separating this point from 5E is 601, the 107th eligible prime; the current default search stops at 563, the 100th.

Commit `a5491d2` replaced a separate 20-prime sieve window with `maxntries`, which also counts failed enlargements. For this input, with no rank growth or enlargement, that reduced the allowance from 200 accepted auxiliary primes to 100. This patch restores the historical 20-prime window as a minimum at both call sites (`std::max(n_aux_stuck, maxntries)`), so callers who pass a larger `maxntries` keep their larger window. It also corrects `tests/tsat` so a supplied upper bound cannot override a failed library result.

The acceptance conditions are unchanged: the sieve still needs full image rank, enlargement still uses exact point division, and exhausted attempts still return unknown. This is a restoration of the search budget for the regression; 20 is a heuristic window, not a guarantee for every isogeny case. The larger window can increase work on unresolved inputs.

An independent integer/Fraction checker enumerates E(F_601): 625 points and 25 [5] images, with `(132,47)` absent from the image. Equivalently, this finite group is `Z/125 x Z/5` and the reduced point has order 125. Good reduction therefore proves that the rational point is not a 5-multiple. The checker also verifies the rational 5-isogeny from h1, its order-five torsion, trivial torsion on h2, and the neighboring point controls.

Validation at `e186f947a83b34d0650bce538404733746b6e12f`, on Ubuntu 24.04 with GCC 13.3, NTL 11.5.1, PARI 2.15.4 and FLINT 3.6:

- A fresh build of this final revision passes `make -j1 check`: 98 output comparisons, including the new `tsat104`. The checks below were run on the previous revision, which differed only by the `std::max` guard and the wording of a test comment.
- A second fresh build with `--disable-mpfp` passes `make -j1 -C tests check_qcurves`: all 21 curve-test programs.
- The new regression fails on h2 P, 5P and 25P against a freshly rebuilt original library. It passes all 16 point/index cases with the patch, including the separate insufficient-budget control.
- The fixed `tsat` driver reports failure against the original library for the bounded issue input.
- A separate table scan passes 682 generator/prime pairs and their 682 true ℓ-multiples, for ℓ = 3, 5, 7. This limited scan found no counterexample to the restored default window.

AI assistance: OpenAI Codex prepared the investigation, implementation, tests and this description. A separate Codex session independently checked the arithmetic certificate, source paths, builds and regression cases. I am submitting this AI-assisted change for review.
