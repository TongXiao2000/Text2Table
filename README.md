
## Introduction

Our project was based on the following paper: [Text-to-Table: A New Way of Information Extraction](https://arxiv.org/abs/2109.02707).

## Dataset

We used the author's dataset from [Google Drive](https://drive.google.com/file/d/1zTfDFCl1nf_giX7IniY5WbXi9tAuEHDn/view?usp=sharing).

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
