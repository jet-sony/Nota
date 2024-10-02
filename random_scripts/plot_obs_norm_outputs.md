### To be placed in `obs_norm.py`
```py
class ObsNormBlock(DSDLayer, tf.keras.layers.Layer):
    num_batch_dims: int
    obs_mean: tf.Variable
    obs_var: tf.Variable
    obs_key: str

    def __init__(self, num_batch_dims: int = 1, obs_key: str = "obs"):
        super().__init__(name="ObsNormBlock")
        self.num_batch_dims = num_batch_dims
        self.obs_key = obs_key

        # HACK IN BLOCK START
        self.counter: int = 0
        self.accumulator_before: list[np.ndarray] = []
        self.accumulator_after: list[np.ndarray] = []
        import tensorflow as tf
        tf.config.run_functions_eagerly(True)
        # HACK IN BLOCK END

    def call(self, inputs: TFND, **kwargs: Any) -> tf.Tensor:
        outputs = (inputs - tf.cast(self.obs_mean, dtype=inputs.dtype)) / (
            tf.cast(tf.sqrt(self.obs_var + 1e-4), dtype=inputs.dtype)
        )

        # HACK IN BLOCK START
        self.counter += 1
        if self.counter > 100:
            self.accumulator_before.append(inputs.numpy())
            self.accumulator_after.append(outputs.numpy())

        if self.counter > 150:
            if self.obs_key == "obs":
                import matplotlib.pyplot as plt
                before_values = np.concatenate(self.accumulator_before, axis=0)
                after_values = np.concatenate(self.accumulator_after, axis=0)

                fig, axs = plt.subplots(2, 2, figsize=(14, 10))

                # mean before
                mean_before = before_values.mean(axis=-1)
                axs[0, 0].bar(range(len(mean_before)), mean_before)
                axs[0, 0].set_title("Mean Before", fontsize=14)

                # variance before
                var_before = before_values.var(axis=-1)
                axs[1, 0].bar(range(len(var_before)), var_before)
                axs[1, 0].set_title("Variance Before", fontsize=14)

                # mean after
                mean_after = after_values.mean(axis=-1)
                axs[0, 1].bar(range(len(mean_after)), mean_after)
                axs[0, 1].set_title("Mean After", fontsize=14)

                # variance after
                var_after = after_values.mean(axis=-1)
                axs[1, 1].bar(range(len(var_after)), var_after)
                axs[1, 1].set_title("Variance After", fontsize=14)

                for ax in axs.flat:
                    ax.tick_params(axis='both', which='major', labelsize=10)
                plt.show()
        # HACK IN BLOCK END

        return outputs
```

### Trajectory Script
```sh
# stat norm
# git checkout 6135518520b77098560bd8657a850140cb01f0df
# python3 /home/dev/sai/apps/gt/scripts/util/generate_eval_config.py --run-id gt-240926-jt-wont-toe --eval-name middle_penalty=000 --model-clocks 1500
# dart run_local -m eval /home/dev/sai/apps/gt/run_configs/temp/eval_config.yaml

# no stat norm
git checkout fb8710ef7004e58dd242d3baffa4d33f57ba6af9
python3 /home/dev/sai/apps/gt/scripts/util/generate_eval_config.py --run-id gt-240925-jt-prior-flaw --eval-name middle_penalty=000 --model-clocks 1500
dart run_local -m eval /home/dev/sai/apps/gt/run_configs/temp/eval_config.yaml
```