# Unifying Heterogeneous Electronic Health Record Systems via Clinical Text-Based Code Embedding
Kyunghoon Hur, Jiyoung Lee, Jungwoo Oh, Wesley Price, Young-Hak Kim, Edward Choi


This repository provides official Pytorch code to implement DescEmb, a code-agnostic EHR predictive model. [[ArXiv](https://arxiv.org/abs/2108.03625)]

# Requirements

* [PyTorch](http://pytorch.org/) version >= 1.8.1
* Python version >= 3.7

# Getting started
## Prepare training data
Given a directory that contains EHR database to be used (we support MIMIC-III and eICU database)  
First, ...
```shell script
$ ...
```

# Examples
## Pre-training a model
### Pre-train a DescEmb model with Masked Language Modeling (MLM)

```shell script
$ python main.py \
    --distributed_world_size $WORLDSIZE \
    --input_path /path/to/data \
    --data $data \
    --task mlm \
    --mlm_prob $percent \
    --model $model
```

### Pre-train a CodeEmb model with Word2Vec

```shell script
$ python main.py \
    --distributed_world_size $WORLDSIZE \
    --input_path /path/to/data \
    --data $data \
    --task w2v
    --model codeemb
```
$data should be set to 'mimic' or 'eicu'

$percent should be set to probability (default: 0.3) of masking for MLM

$model should be set to 'descemb_bert' or 'descemb_rnn'

## Training a new model
Other configurations will set to be default, which were used in the DescEmb paper.

`$descemb` should be 'descemb_bert' or 'descemb_rnn'

`$ratio` should be set to one of [10, 30, 50, 70, 100] (default: 100)

`$value` should be set to one of ['nonconcat', 'VA', 'DSVA', 'DSVA_DPE', 'VC']

`$task` should be set to one of ['readmission', 'mortality', 'los_3day', 'los_7day', 'diagnosis']

Note that `--input-path ` should be the root directory containing preprocessed data.
### Train a new CodeEmb model:

```shell script
$ python main.py \
    --distributed_world_size $WORLDSIZE \
    --input_path /path/to/data \
    --model ehr_model \
    --embed_model codeemb \
    --pred_model rnn \
    --data $data \
    --ratio $ratio \
    --value_embed_type $value \
    --task $task
```
### Train a new DescEmb model:

```shell script
$ python main.py \
    --disrtibuted_world_size $WORLDSIZE \
    --input_path /path/to/data \
    --model ehr_model \
    --embed_model $descemb \
    --pred_model rnn \
    --data $data \
    --ratio $ratio \
    --value_embed_type $value \
    --task $task
```
Note: if you want to train with pre-trained BERT model, add command line parameters `--init_bert_params` or `--init_bert_params_with_freeze`. `--init_bert_params_with_freeze` enables the model to load and freeze BERT parameters.

## Fine-tune a pre-trained model

### Fine-tune a pre-trained CodeEmb model:

```shell script
$ python main.py \
    --distributed_world_size $WORLDSIZE \
    --input_path /path/to/data \
    --model_path /path/to/model.pt \
    --load_pretrained_weights \
    --model ehr_model \
    --embed_model codeemb \
    --pred_model rnn \
    --data $data \
    --ratio $ratio \
    --value_embed_type $value \
    --task $task
```
### Fine-tune a pre-trained DescEmb model:
```shell script
$ python main.py \
    --distributed_world_size $WORLDSIZE \
    --input_path /path/to/data \
    --model_path /path/to/model.pt \
    --load_pretrained_weights \
    --model ehr_model \
    --embed_model $descemb \
    --pred_model rnn \
    --data $data \
    --ratio $ratio \
    --value_embed_type $value \
    --task $task
```

## Transfer a trained model
```shell script
$ python main.py \
    --distributed_world_size $WORLDSIZE \
    --input_path /path/to/data \
    --model_path /path/to/model.pt \
    --transfer \
    --model ehr_model \
    --embed_model $embed_model \
    --pred_model rnn \
    --data $data \
    --ratio $ratio \
    --value_embed_type $value \
    --task $task \
```
Note that `--embed_model` and `pred_model` should be matched with the transferred model.

# License
This repository is MIT-lincensed.

# Citation
Please cite as:
```
@misc{hur2021unifying,
      title={Unifying Heterogenous Electronic Health Records Systems via Text-Based Code Embedding}, 
      author={Kyunghoon Hur and Jiyoung Lee and Jungwoo Oh and Wesley Price and Young-Hak Kim and Edward Choi},
      year={2021},
      eprint={2108.03625},
      archivePrefix={arXiv},
      primaryClass={cs.LG}
}
```