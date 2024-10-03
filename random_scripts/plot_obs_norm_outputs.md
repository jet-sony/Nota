### To be placed in `obs_norm.py`
```py
class ObsNormBlock(DSDLayer, tf.keras.layers.Layer):
    obs_key: str
    num_batch_dims: int

    obs_mean: tf.Variable
    obs_var: tf.Variable
    num_transitions: tf.Variable

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
        if self.counter > 100:  # skip the first few steps to skip initialization data
            self.accumulator_before.append(inputs.numpy())
            self.accumulator_after.append(outputs.numpy())

        if self.counter > 3000:  # gather about 40 seconds of data
            if self.obs_key == "obs":
                import matplotlib.pyplot as plt
                before_values = np.concatenate(self.accumulator_before, axis=0)
                after_values = np.concatenate(self.accumulator_after, axis=0)

                # create the figure and axes (2 rows, 1 column)
                fig, axs = plt.subplots(2, 1, figsize=(120, 40))

                # mean before
                mean_before = before_values.mean(axis=0)
                axs[0].bar(range(len(xtick_labels)), mean_before)
                axs[0].set_title("Mean Before", fontsize=100)
                axs[0].set_yscale('log')
                axs[0].set_xticks(range(len(xtick_labels)))
                axs[0].set_xticklabels(xtick_labels, rotation=90, fontsize=8)
                axs[0].tick_params(axis='y', labelsize=30)

                # mean after
                mean_after = after_values.mean(axis=0)
                axs[1].bar(range(len(xtick_labels)), mean_after)
                axs[1].set_title("Mean After", fontsize=100)
                axs[1].set_yscale('log')
                axs[1].set_xticks(range(len(xtick_labels)))
                axs[1].set_xticklabels(xtick_labels, rotation=90, fontsize=8)
                axs[1].tick_params(axis='y', labelsize=30)

                plt.savefig("mean_before_after.pdf", dpi=300)
                plt.close()

                # create the figure and axes (2 rows, 1 column)
                fig, axs = plt.subplots(2, 1, figsize=(120, 30))

                # var before
                mean_before = before_values.var(axis=0)
                axs[0].bar(range(len(xtick_labels)), mean_before)
                axs[0].set_title("Var Before", fontsize=100)
                axs[0].set_yscale('log')
                axs[0].set_xticks(range(len(xtick_labels)))
                axs[0].set_xticklabels(xtick_labels, rotation=90, fontsize=8)
                axs[0].tick_params(axis='y', labelsize=30)

                # var after
                mean_after = after_values.var(axis=0)
                axs[1].bar(range(len(xtick_labels)), mean_after)
                axs[1].set_title("Var After", fontsize=100)
                axs[1].set_yscale('log')
                axs[1].set_xticks(range(len(xtick_labels)))
                axs[1].set_xticklabels(xtick_labels, rotation=90, fontsize=8)
                axs[1].tick_params(axis='y', labelsize=30)

                plt.savefig("var_before_after.pdf", dpi=300)
                plt.close()
                exit()

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