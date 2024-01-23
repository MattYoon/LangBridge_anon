# LangBridge
Repository for the paper "LANGBRIDGE: Multilingual Reasoning Without Multilingual Supervision".

<p align="center">
  <img src="./figure2.png" width="60%" >
</p>

Paper link: https://arxiv.org/abs/2401.10695


##  🤗Models
### Llemma
- [llemma-langbridge-9b](https://huggingface.co/kaist-ai/llemma-langbrige-9b)
### MetaMath
- [metamath-langbridge-9b](https://huggingface.co/kaist-ai/metamath-langbridge-9b)
- [metamath-langbridge-15b](https://huggingface.co/kaist-ai/metamath-langbridge-15b)
- metamath-langbridge-20b (uploading)
### Code Llama
- [codellama-langbridge-9b](https://huggingface.co/kaist-ai/codellama-langbridge-9b)
- [codellama-langbridge-15b](https://huggingface.co/kaist-ai/codellama-langbridge-15b)
- codellama-langbridge-20b (uploading)
### Orca 2
- [codellama-orac2-9b](https://huggingface.co/kaist-ai/orca2-langbridge-9b)
- [codellama-orca2-15b](https://huggingface.co/kaist-ai/codellama-langbridge-15b)
- codellama-orca2-20b(uploading)


## Install
### Using the Models only
```
pip install -e .
```

### Replicating the evaluation from the paper
```
pip install -e .
cd bigcode-evaluation-harness
pip install -e .
cd ../evaluation-harness
pip install -e.
```

## Usage
### Quick usage example
```python
from transformers import AutoTokenizer
from langbridge import LangBridgeModel

# our pretrained langbridge models all leverage this encoder tokenizer
enc_tokenizer = AutoTokenizer.from_pretrained('kaist-ai/langbridge_encoder_tokenizer') 
lm_tokenizer = AutoTokenizer.from_pretrained('kaist-ai/metamath-langbridge-9b')
model = LangBridgeModel.from_pretrained('kaist-ai/metamath-langbridge-9b').to('cuda')


metamath_template = (
    "Below is an instruction that describes a task. "
    "Write a response that appropriately completes the request.\n\n"
    "### Instruction:\n{instruction}\n\n### Response:\n"
    )
question = "문제: Jimmy는 Ethel이 가진 돈의 두배보다 2달러가 더 많습니다. Ethel이 8달러가 있다고하면, Jimmy는 얼마를 갖고 있나요?  정답: "
prefix =  metamath_template.format(instruction=question)
model.generate_from_prefix(enc_tokenizer, lm_tokenizer, prefix=prefix)
```
```
['If Ethel has 8 dollars, then Jimmy has 2 * 8 + 2 = 18 dollars.\nTherefore, Jimmy has 18 dollars.\n#### 18\nThe answer is: 18']
```

Prompt models as if you were using the original LMs. For example, for Orca 2-langbridge use the Orca 2 template.

### Training Example
```
cd python_scripts
bash scripts/train_lb/metamath.sh
```
- For optimal performance, keep `freeze_encoder=False` for pretrained LMs (trained on unlabeled corpora), and `freeze_encoder=True` for finetuned LMs (trained on labeled corpora). This is explained in section D.1 of the paper.
- The training and validation data should have two columns: `input` and `output`. The `output` should be empty for unlabeled corpora. The code will dynamically split the input and label based on the `input` column alone. See the last sentence of Section 4.1.

### Evaluation Example
```
cd python_scripts
bash scripts/eval/mgsm/metamath-lb-9b.sh
```


## Limitation
LangBridge mostly helps for low-resource languages. If the language model is already proficient in a certain language, adapting it with LangBridge may result in lower performance. Please refer to the paper for the detailed evaluation results.