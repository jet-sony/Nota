# Dart GT Profiling

- `Driver.step` : `/home/dev/sai/libs/sail/python/sail/ctrl/driver.py` @ L85 (0.176)
  - `self._controller.step(self._p_obs)` (0.033)
  - `self._env.step(ctrl_result.env_action)` (0.099)
  - `self._preprocess_obs_for_step(env_result)` (0.045)