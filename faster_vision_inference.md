## Performance w/ policy + 19 identical pretrained

### `jet/faster_inference2`

Vision Runs Preprocessor Structure:

```
VisionProcessor._base_processor = CompositeMultiInputStateProcessor\
  CompositeMultiInputStateProcessor.processors = [
    OnePreprocessorMultipleModels,
    MultiInputStatePreprocessor,
  ]
```

- `OnePreprocessorMultipleModels` broadcasts observations to all models
- `MultiInputStatePreprocessor`  

------------------------

### `jet/faster_inference`

Memory Usage Base: 2.948 GB

#### Before Fix (first 100 steps)

Total time spent in steps: 3.991

#### After Fix (first 100 steps)

Total time spent in steps: 4.20

