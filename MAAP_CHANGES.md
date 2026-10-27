# Changes from upstream

This copy starts from the file tree of https://github.com/SmithB/ATL11 at commit `f78a42f1eab6f249ff7089700dc1f9e0786f7cc1` (master, 2026-10-23), without its git history. Nothing was left out. The code is by Ben Smith and the ATL11 authors; the repository has no license file (`pyproject.toml` points to a missing `LICENSE`, and its classifiers name the MIT license).

## 2026-10-27: `np.isin` instead of `np.in1d`

`np.in1d` was deprecated in NumPy 2.0 and removed in 2.4, so the code stopped with `AttributeError` on a current NumPy. All eleven calls (in `point.py`, `data.py`, `get_xover_data.py`, `check_ATL06_hold_list.py` and `scripts/geoloc_biases_from_xovers.py`) now use `np.isin`. `np.in1d` flattened its first argument and `np.isin` keeps its shape, so `.ravel()` is added where that argument may have more than one dimension.

Results are unchanged: with NumPy 2.3.5, the in-repo test case (`ATL11_test_case/make_ATL11.sh` without `--test_plot`) writes the same ATL11 file before and after the change. All 177 datasets are identical, except `ancillary_data/control`, which records the command line and so the different folder.

Who asked for it: the registration wizard (its readiness check listed the removed NumPy functions and suggested `np.isin`).

## 2026-10-27: NumPy kept below 2.4 in `environment.yml`

NumPy 2.4 also made the conversion of a size-1 array to a scalar an error. On NumPy 2.4.6 and 2.5.4, even with the `np.isin` change above, the test case stops in `data.py:109` (`from_list`) with "ValueError: setting an array element with a sequence" (from "TypeError: only 0-dimensional arrays can be converted to Python scalars"). `environment.yml` now asks for `numpy<2.4`, where the code runs as written. No results change.

Who asked for it: the registration wizard, as the brief's second option (pin NumPy below the release that removed the functions). Its first option alone (the `np.isin` change) was not enough; we reported that to the wizard.

## 2026-10-27: pointCollection installed over HTTPS at a fixed commit

`pyproject.toml` installed pointCollection with `git+ssh://git@github.com/...`, which needs a GitHub SSH key, so `pip install` failed with "Permission denied (publickey)" for anyone without one, build containers included. It now uses `git+https://github.com/smithb/pointCollection.git@1c23042b91892522a3786b627767176f610f69a3`, the same public repository, at the commit that was `main` on 2026-10-18 and that this copy was checked with. No results change.

Who asked for it: our own fix. The wizard's brief asked only to pin the dependency, as a matter of reproducibility, and did not say the SSH URL stops the install.

## Check (2026-10-27)

`conda env update -f environment.yml` into an empty env took 64 s and resolved Python 3.14, NumPy 2.3.5 and pointCollection 1.0.0.0 from commit 1c23042. The in-repo test case (`ATL11_test_case/make_ATL11.sh` without `--test_plot`) took 54 s at 0.28 GB peak. It writes the same ATL11 file as the unchanged upstream code in the same environment: all 177 datasets are identical except `ancillary_data/control` (the command line).
