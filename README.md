**Update 01/12/2023**: for BERTScore evaluation, using `transformers>4.17.0` will lead to different results as in the paper. If you have difficulty replicating the BERTScore results, please try downgrading to `transformers<4.17.0`. In my experiments I used `transformers==3.1.0`. For details please refer to https://github.com/shirley-wu/text_to_table/issues/7

---

## Introduction

This repository is for our ACL2022 paper: [Text-to-Table: A New Way of Information Extraction](https://arxiv.org/abs/2109.02707).

## Requirements

Training requires `fairseq==v0.10.2`, and evaluation requires `sacrebleu==v2.0.0 bert_score==v0.3.11`

Or you can directly install by `pip install -r requirements.txt`.

Note: to avoid potential incompatibility, your fairseq version should be **exactly v0.10.2**, and your python version should be **<3.9**

## Dataset

You can download the four datasets from [Google Drive](https://drive.google.com/file/d/1zTfDFCl1nf_giX7IniY5WbXi9tAuEHDn/view?usp=sharing). If you are interested in preprocessing original table-to-text datasets into our text-to-table datasets, please check [data_preprocessing](data_preprocessing/).

For preprocessing, we use `fairseq` for BPE and binarization. You need to first download a BART model [here](https://github.com/pytorch/fairseq/tree/main/examples/bart), and then use `scripts/preprocess.sh` to preprocess the data. The script has two arguments: the first is the data path and the second is the bart model path, e.g.,
```bash
bash scripts/preprocess.sh data/rotowire/ bart.base/
```
then you'll have BPE-ed files under `data/rotowire` and binary files under `data/rotowire/bins`.

## Training

For each dataset, use `scripts/dataset-name/train_vanilla.sh` to train a vanilla seq2seq model, and use `scripts/dataset-name/train_vanilla.sh` to train a HAD model. The training scripts have two arguments: the first is the data path (NOTE: it's not the path to the binary files) and the second is the bart model path, e.g.,
```bash
bash scripts/rotowire/train_had.sh data/rotowire/ bart.base/
```

Additionally, for Rotowire and WikiTableText, the datasets are very small, so we run experiments with 5 seeds (1, 10, 20, 30, 40) and report the average numbers. Scripts under `scripts/rotowire` and `scripts/wikitabletext` have the seed as the third argument.

Rotowire and WikiBio experiments are run on 8 GPUs. E2E and WikiTableText experiments are run on 1 GPU.

You'll need GPUs that supports `--fp16` (such as V100). If not, please remove the `--fp16` option in the scripts.

## Inference and Evaluation

For each dataset, use `scripts/dataset-name/test_vanilla.sh` to test with vanilla decoding, and use `scripts/dataset-name/test_constraint.sh` to test with table constraint. The test scripts have two arguments: the first is the data path and the second is the checkpoint path (by default it is where your saved checkpoint goes to), e.g.,
```bash
bash scripts/rotowire/test_constraint.sh data/rotowire/ 
```

Similar to training, you'll need GPUs that supports `--fp16`. If not, please remove `--fp16` in the script.


# GPT

## Generate prediction
Run `python generate_gpt_prediction.py --api_key API_KEY --engine_id ENGINE_ID --text_path TEXT_PATH --output_path OUTPUT_PATH`

 * Replace `API_KEY` with open ai api key of the fine-tuned model.
 * Replace `ENGINE_ID` with open ai engine_id of the fine-tuned model.
 * Replace `TEXT_PATH` with the filepath of the text input. The content of the file will be used to generate prediction.
 * Replace `OUTPUT_PATH` with the name of output file for prediction.

See the example inside `generate_test.sh`

## Evaluation
Run `scripts/dataset-name/test_constraint_gpt.sh <true_path> <pred_path>` to test contraints and `scripts/dataset-name/test_vanilla_gpt.sh <true_path> <pred_path>` to test the vanilla. The `<true_path>` is the filepath containing the true value of the output, whereas `<pred_path>` is the prediction file result from `generate_gpt_prediction.py`.

For example, `scripts/e2e/test_vanilla_gpt.sh ./data/e2e/test.data ./data/e2e/test_babbage.pred`

Currently, the GPT evaluation is only available for e2e dataset.