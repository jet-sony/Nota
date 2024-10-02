# To be placed in `obs_norm.py`

Put this in `/home/dev/sai/apps/gt/python/gt/expt/versus/flow/__main__.pyg`

```py
tf.config.run_functions_eagerly(True)
```

Put this in `obs_norm.py`

```py
    counter: int = 0
    accumulator: list[np.ndarray] = []
```

```py
class ObsNormBlock(DSDLayer, tf.keras.layers.Layer):
    num_batch_dims: int
    obs_mean: tf.Variable
    obs_var: tf.Variable
    obs_key: str

    # HACK IN BLOCK START
    counter: int = 0
    accumulator_before: list[np.ndarray] = []
    accumulator_after: list[np.ndarray] = []
    # HACK IN BLOCK END

    def __init__(self, num_batch_dims: int = 1, obs_key: str = "obs"):
        super().__init__(name="ObsNormBlock")
        tf.config.run_functions_eagerly(True)
        self.num_batch_dims = num_batch_dims
        self.obs_key = obs_key

    def call(self, inputs: TFND, **kwargs: Any) -> tf.Tensor:
        outputs = (inputs - tf.cast(self.obs_mean, dtype=inputs.dtype)) / (
            tf.cast(tf.sqrt(self.obs_var + 1e-4), dtype=inputs.dtype)
        )

        # HACK IN BLOCK START
        self.counter += 1
        if self.counter > 100:
            self.accumulator_before.append(inputs.numpy())
            self.accumulator_after.append(outputs.numpy())

        if self.counter > 2000:
            after_values = np.concatenate(self.accumulator_after, axis=0)
            before_values = np.concatenate(self.accumulator_before, axis=0)
            print("Obs key:")
            print(self.obs_key)
            print("Accumulated obs shape (first dimension is batch): ")
            print(after_values.shape)
            print("Obs means before: ")
            print(before_values.mean(axis=-1))
            print("Obs var before: ")
            print(before_values.var(axis=-1))
            print("Obs means after: ")
            print(after_values.mean(axis=-1))
            print("Obs var after: ")
            print(after_values.var(axis=-1))
            exit()
        # HACK IN BLOCK END

        return outputs

```