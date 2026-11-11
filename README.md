# athanor-software-patches

Fixes for research software that we found and verified while doing our own computations. These are **patches against upstream projects, not forks or replacements**. We submit them upstream, and this repository is a stable public record and a way to use a fix before it is merged.

| Folder | Upstream project | Problem | Status |
|---|---|---|---|
| [`eclib-104-saturation/`](eclib-104-saturation/) | [eclib](https://github.com/JohnCremona/eclib) by John Cremona: mwrank 2-descent and saturation, used by SageMath and the LMFDB | [Issue #104](https://github.com/JohnCremona/eclib/issues/104): 5-saturation could not be certified with a 5-isogeny. Commit a5491d2 (2022) halved the auxiliary-prime window from 200 to 100. The `tsat` driver also reported success after a failure. | The patch restores a minimum 20-prime window (`std::max(20, maxntries)`) and fixes the driver; regression test added. The proof condition is unchanged. All 98 eclib tests pass, and 1,364 isogeny cases were scanned without failure. A ready-to-build fork is at [eclib-athanor-edition](https://github.com/iwasborninbali/eclib-athanor-edition) (default branch `athanor-edition`; topic branch `fix-104-saturation-window`). Upstream PR in preparation. |
| [`sagemath-integral-points/`](sagemath-integral-points/) | [SageMath](https://github.com/sagemath/sage), `EllipticCurve_rational_field.integral_points()` | When the lattice-reduction inequality fails, the code substitutes an unjustified bound (`d_L_0 = 10*(T**2*Q)`). This can lose points: we reproduced 12 missing points on 11 curves. Known since Cremona's 2013 comment on #10973; see also #10152, #27015 and the open PR #43044 by Manvir Jaswal. | The patch replaces the bound with the minimum Gram–Schmidt length, grows scale and precision until the inequality holds, and fails closed. 27 doctests and 317 database comparisons pass. **We support PR #43044 rather than competing with it;** this patch and the evidence are offered to that work. |

**How to apply.** Each patch states the upstream commit it was made against. For example, for eclib at `e186f947`:

```sh
git clone https://github.com/JohnCremona/eclib && cd eclib && git checkout e186f947 && git apply ../fix.patch
```

**License.** Each patch is a derivative of its upstream project and is distributed under the same license as that project (see the upstream COPYING or LICENSE file). The documentation here is CC BY 4.0.

**Provenance.**
- The patches, tests and analyses were prepared by AI research agents (OpenAI Codex; Anthropic Claude as orchestrator) under the direction of Aleksei Kudriashov.
- A separate AI session independently verified each one. Human expert review lies with the upstream maintainers.
- Credit for the original defect reports belongs to the people named above.
