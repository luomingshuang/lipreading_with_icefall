## Results

### LibriSpeech BPE training results (Pruned Transducer)

Conformer encoder + non-current decoder. The decoder
contains only an embedding layer, a Conv1d (with kernel size 2) and a linear
layer (to transform tensor dim).

#### 2022-03-12

[pruned_transducer_stateless](./pruned_transducer_stateless)

Using commit `1603744469d167d848e074f2ea98c587153205fa`.
See <https://github.com/k2-fsa/icefall/pull/248>

The WERs are:

|                                     | test-clean | test-other | comment                                  |
|-------------------------------------|------------|------------|------------------------------------------|
| greedy search (max sym per frame 1) | 2.62       | 6.37       | --epoch 42, --avg 11, --max-duration 100 |
| greedy search (max sym per frame 2) | 2.62       | 6.37       | --epoch 42, --avg 11, --max-duration 100 |
| greedy search (max sym per frame 3) | 2.62       | 6.37       | --epoch 42, --avg 11, --max-duration 100 |
| modified beam search (beam size 4)  | 2.56       | 6.27       | --epoch 42, --avg 11, --max-duration 100 |
| beam search (beam size 4)           | 2.57       | 6.27       | --epoch 42, --avg 11, --max-duration 100 |

The decoding time for `test-clean` and `test-other` is given below:
(A V100 GPU with 32 GB RAM is used for decoding. Note: Not all GPU RAM is used during decoding.)

| decoding method | test-clean (seconds) | test-other (seconds)|
|---|---:|---:|
| greedy search (--max-sym-per-frame=1) | 160 | 159 |
| greedy search (--max-sym-per-frame=2) | 184 | 177 |
| greedy search (--max-sym-per-frame=3) | 210 | 213 |
| modified beam search (--beam-size 4)| 273 | 269 |
|beam search (--beam-size 4) | 2741 | 2221 |

We recommend you to use `modified_beam_search`.

Training command:

```bash
cd egs/librispeech/ASR/
./prepare.sh

export CUDA_VISIBLE_DEVICES="0,1,2,3,4,5,6,7"

. path.sh

./pruned_transducer_stateless/train.py \
  --world-size 8 \
  --num-epochs 60 \
  --start-epoch 0 \
  --exp-dir pruned_transducer_stateless/exp \
  --full-libri 1 \
  --max-duration 300 \
  --prune-range 5 \
  --lr-factor 5 \
  --lm-scale 0.25
```

The tensorboard training log can be found at
<https://tensorboard.dev/experiment/WKRFY5fYSzaVBHahenpNlA/>

The command for decoding is:

```bash
epoch=42
avg=11
sym=1

# greedy search

./pruned_transducer_stateless/decode.py \
  --epoch $epoch \
  --avg $avg \
  --exp-dir ./pruned_transducer_stateless/exp \
  --max-duration 100 \
  --decoding-method greedy_search \
  --beam-size 4 \
  --max-sym-per-frame $sym

# modified beam search
./pruned_transducer_stateless/decode.py \
  --epoch $epoch \
  --avg $avg \
  --exp-dir ./pruned_transducer_stateless/exp \
  --max-duration 100 \
  --decoding-method modified_beam_search \
  --beam-size 4

# beam search
# (not recommended)
./pruned_transducer_stateless/decode.py \
  --epoch $epoch \
  --avg $avg \
  --exp-dir ./pruned_transducer_stateless/exp \
  --max-duration 100 \
  --decoding-method beam_search \
  --beam-size 4
```

You can find a pre-trained model, decoding logs, and decoding results at
<https://huggingface.co/csukuangfj/icefall-asr-librispeech-pruned-transducer-stateless-2022-03-12>

#### 2022-02-18

[pruned_transducer_stateless](./pruned_transducer_stateless)


The WERs are

|                           | test-clean | test-other | comment                                  |
|---------------------------|------------|------------|------------------------------------------|
| greedy search             | 2.85       | 6.98       | --epoch 28, --avg 15, --max-duration 100 |

The training command for reproducing is given below:

```
export CUDA_VISIBLE_DEVICES="0,1,2,3"

./pruned_transducer_stateless/train.py \
  --world-size 4 \
  --num-epochs 30 \
  --start-epoch 0 \
  --exp-dir pruned_transducer_stateless/exp \
  --full-libri 1 \
  --max-duration 300 \
  --prune-range 5 \
  --lr-factor 5 \
  --lm-scale 0.25 \
```

The tensorboard training log can be found at
<https://tensorboard.dev/experiment/ejG7VpakRYePNNj6AbDEUw/#scalars>

The decoding command is:
```
epoch=28
avg=15

## greedy search
./pruned_transducer_stateless/decode.py \
  --epoch $epoch \
  --avg $avg \
  --exp-dir pruned_transducer_stateless/exp \
  --max-duration 100
```


### LibriSpeech BPE training results (Transducer)

#### Conformer encoder + embedding decoder

Conformer encoder + non-recurrent decoder. The decoder
contains only an embedding layer and a Conv1d (with kernel size 2).

See

- [./transducer_stateless](./transducer_stateless)
- [./transducer_stateless_multi_datasets](./transducer_stateless_multi_datasets)

##### 2022-03-01

Using commit `2332ba312d7ce72f08c7bac1e3312f7e3dd722dc`.

It uses [GigaSpeech](https://github.com/SpeechColab/GigaSpeech)
as extra training data. 20% of the time it selects a batch from L subset of
GigaSpeech and 80% of the time it selects a batch from LibriSpeech.

The WERs are

|                                     | test-clean | test-other | comment                                  |
|-------------------------------------|------------|------------|------------------------------------------|
| greedy search (max sym per frame 1) | 2.64       | 6.55       | --epoch 39, --avg 15, --max-duration 100 |
| modified beam search (beam size 4)  | 2.61       | 6.46       | --epoch 39, --avg 15, --max-duration 100 |

The training command for reproducing is given below:

```bash
cd egs/librispeech/ASR/
./prepare.sh
./prepare_giga_speech.sh

export CUDA_VISIBLE_DEVICES="0,1,2,3"

./transducer_stateless_multi_datasets/train.py \
  --world-size 4 \
  --num-epochs 40 \
  --start-epoch 0 \
  --exp-dir transducer_stateless_multi_datasets/exp-full-2 \
  --full-libri 1 \
  --max-duration 300 \
  --lr-factor 5 \
  --bpe-model data/lang_bpe_500/bpe.model \
  --modified-transducer-prob 0.25 \
  --giga-prob 0.2
```

The tensorboard training log can be found at
<https://tensorboard.dev/experiment/xmo5oCgrRVelH9dCeOkYBg/>

The decoding command is:

```bash
epoch=39
avg=15
sym=1

# greedy search
./transducer_stateless_multi_datasets/decode.py \
  --epoch $epoch \
  --avg $avg \
  --exp-dir transducer_stateless_multi_datasets/exp-full-2 \
  --bpe-model ./data/lang_bpe_500/bpe.model \
  --max-duration 100 \
  --context-size 2 \
  --max-sym-per-frame $sym

# modified beam search
./transducer_stateless_multi_datasets/decode.py \
  --epoch $epoch \
  --avg $avg \
  --exp-dir transducer_stateless_multi_datasets/exp-full-2 \
  --bpe-model ./data/lang_bpe_500/bpe.model \
  --max-duration 100 \
  --context-size 2 \
  --decoding-method modified_beam_search \
  --beam-size 4
```

You can find a pretrained model by visiting
<https://huggingface.co/csukuangfj/icefall-asr-librispeech-transducer-stateless-multi-datasets-bpe-500-2022-03-01>


##### 2022-02-07

Using commit `a8150021e01d34ecbd6198fe03a57eacf47a16f2`.


The WERs are

|                                     | test-clean | test-other | comment                                  |
|-------------------------------------|------------|------------|------------------------------------------|
| greedy search (max sym per frame 1) | 2.67       | 6.67       | --epoch 63, --avg 19, --max-duration 100 |
| greedy search (max sym per frame 2) | 2.67       | 6.67       | --epoch 63, --avg 19, --max-duration 100 |
| greedy search (max sym per frame 3) | 2.67       | 6.67       | --epoch 63, --avg 19, --max-duration 100 |
| modified beam search (beam size 4)  | 2.67       | 6.57       | --epoch 63, --avg 19, --max-duration 100 |


The training command for reproducing is given below:

```
cd egs/librispeech/ASR/
./prepare.sh
export CUDA_VISIBLE_DEVICES="0,1,2,3"
./transducer_stateless/train.py \
  --world-size 4 \
  --num-epochs 76 \
  --start-epoch 0 \
  --exp-dir transducer_stateless/exp-full \
  --full-libri 1 \
  --max-duration 300 \
  --lr-factor 5 \
  --bpe-model data/lang_bpe_500/bpe.model \
  --modified-transducer-prob 0.25
```

The tensorboard training log can be found at
<https://tensorboard.dev/experiment/qgvWkbF2R46FYA6ZMNmOjA/#scalars>

The decoding command is:
```
epoch=63
avg=19

## greedy search
for sym in 1 2 3; do
  ./transducer_stateless/decode.py \
    --epoch $epoch \
    --avg $avg \
    --exp-dir transducer_stateless/exp-full \
    --bpe-model ./data/lang_bpe_500/bpe.model \
    --max-duration 100 \
    --max-sym-per-frame $sym
done

## modified beam search

./transducer_stateless/decode.py \
  --epoch $epoch \
  --avg $avg \
  --exp-dir transducer_stateless/exp-full \
  --bpe-model ./data/lang_bpe_500/bpe.model \
  --max-duration 100 \
  --context-size 2 \
  --decoding-method modified_beam_search \
  --beam-size 4
```

You can find a pretrained model by visiting
<https://huggingface.co/csukuangfj/icefall-asr-librispeech-transducer-stateless-bpe-500-2022-02-07>


#### Conformer encoder + LSTM decoder
Using commit `8187d6236c2926500da5ee854f758e621df803cc`.

Conformer encoder + LSTM decoder.

The best WER is

|     | test-clean | test-other |
|-----|------------|------------|
| WER | 3.07       | 7.51       |

using `--epoch 34 --avg 11` with **greedy search**.

The training command to reproduce the above WER is:

```
export CUDA_VISIBLE_DEVICES="0,1,2,3"

./transducer/train.py \
  --world-size 4 \
  --num-epochs 35 \
  --start-epoch 0 \
  --exp-dir transducer/exp-lr-2.5-full \
  --full-libri 1 \
  --max-duration 180 \
  --lr-factor 2.5
```

The decoding command is:

```
epoch=34
avg=11

./transducer/decode.py \
  --epoch $epoch \
  --avg $avg \
  --exp-dir transducer/exp-lr-2.5-full \
  --bpe-model ./data/lang_bpe_500/bpe.model \
  --max-duration 100
```

You can find the tensorboard log at: <https://tensorboard.dev/experiment/D7NQc3xqTpyVmWi5FnWjrA>


### LibriSpeech BPE training results (Conformer-CTC)

#### 2021-11-09

The best WER, as of 2021-11-09, for the librispeech test dataset is below
(using HLG decoding + n-gram LM rescoring + attention decoder rescoring):

|     | test-clean | test-other |
|-----|------------|------------|
| WER | 2.42       | 5.73       |

Scale values used in n-gram LM rescoring and attention rescoring for the best WERs are:
| ngram_lm_scale | attention_scale |
|----------------|-----------------|
| 2.0            | 2.0             |


To reproduce the above result, use the following commands for training:

```
cd egs/librispeech/ASR/conformer_ctc
./prepare.sh
export CUDA_VISIBLE_DEVICES="0,1,2,3"
./conformer_ctc/train.py \
  --exp-dir conformer_ctc/exp_500_att0.8 \
  --lang-dir data/lang_bpe_500 \
  --att-rate 0.8 \
  --full-libri 1 \
  --max-duration 200 \
  --concatenate-cuts 0 \
  --world-size 4 \
  --bucketing-sampler 1 \
  --start-epoch 0 \
  --num-epochs 90
# Note: It trains for 90 epochs, but the best WER is at epoch-77.pt
```

and the following command for decoding

```
./conformer_ctc/decode.py \
  --exp-dir conformer_ctc/exp_500_att0.8 \
  --lang-dir data/lang_bpe_500 \
  --max-duration 30 \
  --concatenate-cuts 0 \
  --bucketing-sampler 1 \
  --num-paths 1000 \
  --epoch 77 \
  --avg 55 \
  --method attention-decoder \
  --nbest-scale 0.5
```

You can find the pre-trained model by visiting
<https://huggingface.co/csukuangfj/icefall-asr-librispeech-conformer-ctc-jit-bpe-500-2021-11-09>

The tensorboard log for training is available at
<https://tensorboard.dev/experiment/hZDWrZfaSqOMqtW0NEfXKg/#scalars>


#### 2021-08-19
(Wei Kang): Result of https://github.com/k2-fsa/icefall/pull/13

TensorBoard log is available at https://tensorboard.dev/experiment/GnRzq8WWQW62dK4bklXBTg/#scalars

Pretrained model is available at https://huggingface.co/pkufool/icefall_asr_librispeech_conformer_ctc

The best decoding results (WER) are listed below, we got this results by averaging models from epoch 15 to 34, and using `attention-decoder` decoder with num_paths equals to 100.

||test-clean|test-other|
|--|--|--|
|WER| 2.57% | 5.94% |

To get more unique paths, we scaled the lattice.scores with 0.5 (see https://github.com/k2-fsa/icefall/pull/10#discussion_r690951662 for more details), we searched the lm_score_scale and attention_score_scale for best results, the scales that produced the WER above are also listed below.

||lm_scale|attention_scale|
|--|--|--|
|test-clean|1.3|1.2|
|test-other|1.2|1.1|

You can use the following commands to reproduce our results:

```bash
git clone https://github.com/k2-fsa/icefall
cd icefall

# It was using ef233486, you may not need to switch to it
# git checkout ef233486

cd egs/librispeech/ASR
./prepare.sh

export CUDA_VISIBLE_DEVICES="0,1,2,3"
python conformer_ctc/train.py --bucketing-sampler True \
                              --concatenate-cuts False \
                              --max-duration 200 \
                              --full-libri True \
                              --world-size 4 \
                              --lang-dir data/lang_bpe_5000

python conformer_ctc/decode.py --nbest-scale 0.5 \
                               --epoch 34 \
                               --avg 20 \
                               --method attention-decoder \
                               --max-duration 20 \
                               --num-paths 100 \
                               --lang-dir data/lang_bpe_5000
```

### LibriSpeech training results (Tdnn-Lstm)
#### 2021-08-24

(Wei Kang): Result of phone based Tdnn-Lstm model.

Icefall version: https://github.com/k2-fsa/icefall/commit/caa0b9e9425af27e0c6211048acb55a76ed5d315

Pretrained model is available at https://huggingface.co/pkufool/icefall_asr_librispeech_tdnn-lstm_ctc

The best decoding results (WER) are listed below, we got this results by averaging models from epoch 19 to 14, and using `whole-lattice-rescoring` decoding method.

||test-clean|test-other|
|--|--|--|
|WER| 6.59% | 17.69% |

We searched the lm_score_scale for best results, the scales that produced the WER above are also listed below.

||lm_scale|
|--|--|
|test-clean|0.8|
|test-other|0.9|