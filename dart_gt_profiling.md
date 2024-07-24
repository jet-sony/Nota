# GT Profiling

### 1 Pretrained across 19 cars

- `Driver.step` : `/home/dev/sai/libs/sail/python/sail/ctrl/driver.py` @ L85 (0.176)
  - `self._controller.step(self._p_obs)` (0.033)
  - `self._env.step(ctrl_result.env_action)` | `APIVisionEnv.step()` : `/home/dev/sai/apps/gt/python/gt/env/gt_env.py` @ L500 (0.099)
    - Everything else (0.009)
    - `self._act_and_get_obs_extended(action, prev_obs)` | `/home/dev/sai/apps/gt/python/gt/env/gt_env.py` @ L572 | `/home/dev/sai/apps/gt/python/gt/env/gt_env.py` @ L1481 (0.09)
      - `super()._act_and_get_obs(action, prev_obs)` : `/home/dev/sai/apps/gt/python/gt/env/gt_env.py` @ L1257 (0.084)
        - `self.rest.execute_and_wait_for_obs` (0.081)
        - `self._add_derived_to_obs(raw_obs, prev_obs)` (0.003)
      - `self._add_latest_image_to_obs(obs)` | `/home/dev/sai/apps/gt/python/gt/env/gt_env.py` @ L1487 (0.005)
    
  - `self._preprocess_obs_for_step(env_result)` (0.045)