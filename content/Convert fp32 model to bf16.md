```python
from transformers import AutoModelForCausalLM, AutoTokenizer  
import torch  
  
# Load the model in float32  
model_name = "output_dir"
model = AutoModelForCausalLM.from_pretrained(model_name, torch_dtype="float32")  
#tokenizer = AutoTokenizer.from_pretrained(model_name)  
  
# Convert the model to bfloat16  
model = model.to(dtype=torch.bfloat16)  
  
# Save the model in bfloat16  
save_path = "./bfloat16_model"
model.save_pretrained(save_path)  
#tokenizer.save_pretrained(save_path)
```