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
    def call(self, inputs: TFND, **kwargs: Any) -> tf.Tensor:
        outputs = (inputs - tf.cast(self.obs_mean, dtype=inputs.dtype)) / (
            tf.cast(tf.sqrt(self.obs_var + 1e-4), dtype=inputs.dtype)
        )

        self.counter += 1
        if self.counter > 10:
            self.accumulator.append(outputs.numpy())

        if self.counter > 1000:
            accumulated_values = np.concatenate(self.accumulator, axis=0)
            print("Obs key:")
            print(self.obs_key)
            print("Accumulated obs shape (first dimension is batch): ")
            print(accumulated_values.shape)
            print("Obs means: ")
            print(accumulated_values.mean())
            print("Obs var: ")
            print(accumulated_values.var())
            exit()

        return outputs

```