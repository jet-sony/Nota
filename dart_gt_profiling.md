# GT Profiling

## Script

```py
self.counts = 0
self.timestats = 0.0

cps = [time.time()]
cps.append(time.time())

looptimes = np.array(cps)[1:] - np.array(cps)[:-1]
self.timestats = (self.timestats * self.counts  + np.array([*looptimes, looptimes.sum()])) / (self.counts + 1)
self.counts += 1
print(self.timestats)

```

### 1 Pretrained across 19 cars

- `Driver.step` : `/home/dev/sai/libs/sail/python/sail/ctrl/driver.py` @ L85 (0.180)
  - `self._controller.step(self._p_obs)` (0.0470)
  - `self._env.step(ctrl_result.env_action)` | `APIVisionEnv.step()` : `/home/dev/sai/apps/gt/python/gt/env/gt_env.py` @ L500 (0.10)
    - Everything else (0.009)
    - `self._act_and_get_obs_extended(action, prev_obs)` | `/home/dev/sai/apps/gt/python/gt/env/gt_env.py` @ L572 | `/home/dev/sai/apps/gt/python/gt/env/gt_env.py` @ L1481 (0.0986)
      - `super()._act_and_get_obs(action, prev_obs)` : `/home/dev/sai/apps/gt/python/gt/env/gt_env.py` @ L1257 (0.090)
        - `self.rest.execute_and_wait_for_obs` (0.087)
        - `self._add_derived_to_obs(raw_obs, prev_obs)` (0.0032)
      - `self._add_latest_image_to_obs(obs)` | `/home/dev/sai/apps/gt/python/gt/env/gt_env.py` @ L1487 (0.005)
    
  - `self._preprocess_obs_for_step(env_result)` (0.029)

### BIAI

- `Driver.step` : `/home/dev/sai/libs/sail/python/sail/ctrl/driver.py` @ L85 (0.141)
  - `self._controller.step(self._p_obs)` | `MultiModelController.step()` : `/home/dev/sai/libs/sail/python/sail/unstable/ctrl/multi_model_controller.py` @ L115 (0.0187)
    - `name_to_inputs` splicing @ L127 (negligible)
    - `self._forwarder.step` @ L135 (0.007)
    - `self._action_merger.step` @ L137 (0.0008)
    - `aux.update(merged_states) @ L149 (0.0005)
    - `aux.update(self._pp_result.aux)` @ L158 (0.0083)
  - `self._env.step(ctrl_result.env_action)` | `APIVisionEnv.step()` : `/home/dev/sai/apps/gt/python/gt/env/gt_env.py` @ L500 (0.101)
    - Everything else (0.009)
    - `self._act_and_get_obs_extended(action, prev_obs)` | `/home/dev/sai/apps/gt/python/gt/env/gt_env.py` @ L572 | `/home/dev/sai/apps/gt/python/gt/env/gt_env.py` @ L1481 (0.0995)
      - `super()._act_and_get_obs(action, prev_obs)` : `/home/dev/sai/apps/gt/python/gt/env/gt_env.py` @ L1257 (0.0988)
        - `self.rest.execute_and_wait_for_obs` (0.088)
        - `self._add_derived_to_obs(raw_obs, prev_obs)` (0.0037)
      - `self._add_latest_image_to_obs(obs)` | `/home/dev/sai/apps/gt/python/gt/env/gt_env.py` @ L1487 (0.005)
    
  - `self._preprocess_obs_for_step(env_result)` (0.0169)