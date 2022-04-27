# Japanese-CLIP
![rinna-icon](./data/rinna.png)

This repository contains Japanese [CLIP](https://arxiv.org/abs/2103.00020) variants by [rinna Co., Ltd](https://rinna.co.jp/).

| Table of Contents |
|-|
| [Available Models](#Available-Models) |
| [Usage](#Usage) |



## Available Models

| Model Name | TOP1\* |  TOP5\* |
|:--------:|:--:|:---:|
| [rinna/japanese-cloob-vit-b-16](https://huggingface.co/rinna/japanese-cloob-vit-b-16) | 48.37 | 65.40 | 
| [rinna/japanese-clip-vit-b-16](https://huggingface.co/rinna/japanese-clip-vit-b-16) | 41.09 | 61.83 |
| [sonoisa/clip-vit-b-32-japanese-v1](https://huggingface.co/sonoisa/clip-vit-b-32-japanese-v1) | 38.38 | 59.93 |
| [multilingual-CLIP](https://huggingface.co/sentence-transformers/clip-ViT-B-32-multilingual-v1) | 14.09 | 26.43 |
*\*Zero-shot ImageNet validation set accuracy. Used `{japanese_class_name}の写真` for text prompts*

## Usage

1. Install package
```shell
$ pip install git+https://github.com/rinnakk/japanese-clip.git
```
2. Run
```python
import torch
import japanese_clip as ja_clip
from PIL import Image

device = "cuda" if torch.cuda.is_available() else "cpu"
# ja_clip.available_models()
# ['rinna/japanese-clip-vit-b-16', 'rinna/japanese-cloob-vit-b-16']
model, preprocess = ja_clip.load("rinna/japanese-clip-vit-b-16", cache_dir="/tmp/japanese_clip", device=device)
tokenizer = ja_clip.load_tokenizer()

image = preprocess(Image.open("./data/dog.jpeg")).unsqueeze(0).to(device)
encodings = ja_clip.tokenize(
    texts=["犬", "猫", "象"],
    max_seq_len=77,
    device=device,
    tokenizer=tokenizer, # this is optional. if you don't pass, load tokenizer each time
)

with torch.no_grad():
    image_features = model.get_image_features(image)
    text_features = model.get_text_features(**encodings)
    
    text_probs = (100.0 * image_features @ text_features.T).softmax(dim=-1)

print("Label probs:", text_probs)  # prints: [[1.0, 0.0, 0.0]]
```

## TODO
- [ ] Upload models to huggingface
- [ ] Release first version
- [ ] Test downloading models from hf and inference