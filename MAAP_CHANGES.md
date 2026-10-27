# Changes from upstream

This copy starts from the file tree of https://github.com/SmithB/ATL11 at commit `f78a42f1eab6f249ff7089700dc1f9e0786f7cc1` (master, 2026-10-23), without its git history. Nothing was left out. The code is by Ben Smith and the ATL11 authors; the repository has no license file (`pyproject.toml` points to a missing `LICENSE`, and its classifiers name the MIT license).

## 2026-10-27: `np.isin` instead of `np.in1d`

`np.in1d` was deprecated in NumPy 2.0 and removed in 2.4, so the code stopped with `AttributeError` on a current NumPy. All eleven calls (in `point.py`, `data.py`, `get_xover_data.py`, `check_ATL06_hold_list.py` and `scripts/geoloc_biases_from_xovers.py`) now use `np.isin`. `np.in1d` flattened its first argument and `np.isin` keeps its shape, so `.ravel()` is added where that argument may have more than one dimension.

Results are unchanged: with NumPy 2.3.5, the in-repo test case (`ATL11_test_case/make_ATL11.sh` without `--test_plot`) writes the same ATL11 file before and after the change. All 177 datasets are identical, except `ancillary_data/control`, which records the command line and so the different folder.

Who asked for it: the registration wizard (its readiness check listed the removed NumPy functions and suggested `np.isin`).
