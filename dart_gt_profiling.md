# Dart GT Profiling

- `Driver.step` : `/home/dev/sai/libs/sail/python/sail/ctrl/driver.py` @ L85 (0.176)
  - `self._controller.step(self._p_obs)` (0.033)
  - `self._env.step(ctrl_result.env_action)` | `APIVisionEnv.step()` : `/home/dev/sai/apps/gt/python/gt/env/gt_env.py` @ L500 (0.099)
    - Everything else (0.009)
    - `self._act_and_get_obs_extended(action, prev_obs)` | `/home/dev/sai/apps/gt/python/gt/env/gt_env.py` @ L572 (0.09)
    
  - `self._preprocess_obs_for_step(env_result)` (0.045)