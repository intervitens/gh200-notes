- Replace the `zero_to_fp32.py` script with a more efficient version from a [pull request](https://github.com/microsoft/DeepSpeed/pull/6658)
```
cd saves/example_checkpoint
rm zero_to_fp32.py
wget https://github.com/microsoft/DeepSpeed/raw/27e8ae27e766eeaa29ee5e8d068b14234b040db2/deepspeed/utils/zero_to_fp32.py
```
- Convert the checkpoint to fp32 weights
```
mkdir output_dir
python zero_to_fp32.py . output_dir/ --safe_serialization
```
- Convert the fp32 model to bf16 using transformers.
- `bf16.py` is [[Convert fp32 model to bf16]]
```
cp config.json generation_config.json output_dir
python3 bf16.py
```
- Move tokenizer and other files to bf16 model folder (exact tokenizer files depend on the model)
```
cp README.md special_tokens_map.json tokenizer_config.json tokenizer.json trainer_log.jsonl train_results.json all_results.json vocab.json merges.txt added_tokens.json training_loss.png bfloat16_model
```