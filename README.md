# Multilingual-Adapters

## Introduction

We provide an implementation of the adapters for multilingual transformer systems. This was built on top of fairseq.

## Training a multilingual model with adapters

Our methods introduce new hyper-parameters:

+ `--freeze-layers` which freezes the parameters of the transformer layers;
+ `--freeze-embeddings` which freezes the embedding layer parameters;
+ `--adapters-type` which controls the type of the adapters (language-pair, source, target, or a combination of source (in the encoder) and target (in the decoder);
+ `--languages-adapters` which controls which languages will have adapters;
+ `--lang-pairs-adapters` which controls which language-pairs will have adapters;
+ `--adapter-projection-dim` which controls the hidden dimension of the adapters.

Below is an example of injecting adapters on top of a fully-shared multilingual system:


```bash
DATA_BIN=<path to binarized data>
CHECKPOINTS_SHARED_MODEL=<path to the checkpoint of the fully shared model>
SAVE_DIR=<directory where the model is saved>

LANG_PAIRS ="en-bg,en-cs,en-da,en-de,en-el,en-es,en-et,en-fi,en-fr,en-hu,en-it,en-lt,en-lv,en-nl,en-pl,en-pt,en-ro,en-sk,en-sl,en-sv,en-mt,en-hr,en-ga,bg-en,cs-en,da-en,de-en,el-en,es-en,et-en,fi-en,fr-en,hu-en,it-en,lt-en,lv-en,nl-en,pl-en,pt-en,ro-en,sk-en,sl-en,sv-en,mt-en,hr-en,ga-en"

ADAPTERS_DIM=<hidden dimension of the adapters>
ADAPTERS_CONDITION=<language-pairs, source, target or source+target>
LANGUAGES_ADAPTERS="en,bg,cs,da,de,el,es,et,fi,fr,hu,it,lt,lv,nl,pl,pt,ro,sk,sl,sv,mt,hr,ga"

fairseq-train ${DATA_BIN} \
    --fp16 \
    --ddp-backend=no_c10d \
    --task multilingual_translation \
    --lang-pairs  \
    --arch multilingual_adapters \
    --share-encoders \
    --share-decoders \
    --share-decoder-input-output-embed \
    --adapter-projection-dim ${ADAPTERS_DIM} \
    --freeze-embeddings \
    --freeze-layers \
    --adapters-type ${ADAPTERS_CONDITION} \
    --languages-adapters ${LANGUAGES_ADAPTERS} \
    --reset-optimizer \
    --optimizer adam \
    --adam-betas '(0.9, 0.98)' \
    --lr 5e-4 \
    --lr-scheduler inverse_sqrt \
    --min-lr '1e-09' \
    --warmup-updates 16000 \
    --warmup-init-lr '1e-07' \
    --criterion label_smoothed_cross_entropy \
    --label-smoothing 0.1 \
    --dropout 0.3 \
    --weight-decay 0.0001 \
    --max-tokens 4096 \
    --encoder-langtok tgt \
    --restore-file ${CHECKPOINTS_SHARED_MODEL} \
    --save-dir ${SAVE_DIR} \
```


## Inference command

```bash
DATA_BIN=<path to binarized data>
CHECKPOINT=<path to checkpointl>

LANG_PAIRS ="en-bg,en-cs,en-da,en-de,en-el,en-es,en-et,en-fi,en-fr,en-hu,en-it,en-lt,en-lv,en-nl,en-pl,en-pt,en-ro,en-sk,en-sl,en-sv,en-mt,en-hr,en-ga,bg-en,cs-en,da-en,de-en,el-en,es-en,et-en,fi-en,fr-en,hu-en,it-en,lt-en,lv-en,nl-en,pl-en,pt-en,ro-en,sk-en,sl-en,sv-en,mt-en,hr-en,ga-en"
SOURCE_LANG="en"
TARGET_LANG="bg"


        fairseq-generate $DATA_BIN --task multilingual_translation \
        --lang-pairs ${LANG_PAIRS} \
        --path ${CHECKPOINT} \
        --source-lang ${SOURCE_LANG} \
        --target-lang ${TARGET_LANG} \
        --model-overrides "{'source_lang':'${SOURCE_LANG}','target_lang':'${TARGET_LANG}'}" \
        --beam 5 \
        --encoder-langtok tgt \
        --remove-bpe 
```


## Citation
```bibtex
@article
}
```
