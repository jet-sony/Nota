# To be placed in `obs_norm.py`

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
            print(self.obs_mean.numpy())
            exit()
            self.accumulator_before.append(inputs.numpy())
            self.accumulator_after.append(outputs.numpy())

        if self.counter > 200:
            before_values = np.concatenate(self.accumulator_before, axis=0)
            after_values = np.concatenate(self.accumulator_after, axis=0)
            print("Obs key:")
            print(self.obs_key)
            print("Accumulated obs shape (first dimension is batch): ")
            print(after_values.shape)
            print("Obs means before: ")
            print(before_values.mean(axis=-1))
            print("Obs means after: ")
            print(after_values.mean(axis=-1))
            print("Obs var before: ")
            print(before_values.var(axis=-1))
            print("Obs var after: ")
            print(after_values.var(axis=-1))
            exit()
        # HACK IN BLOCK END

        return outputs
 h
```