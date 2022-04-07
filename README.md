<div align="center">
<img src="https://raw.githubusercontent.com/k2-fsa/icefall/master/docs/source/_static/logo.png" width=168>
</div>

## Installation

Please refer to <https://icefall.readthedocs.io/en/latest/installation/index.html>
for installation.

## Recipes

Please refer to <https://icefall.readthedocs.io/en/latest/recipes/index.html>
for more information.

We provide four recipes at present:

  - [yesno][yesno]
  - [GRID][grid]

### yesno
  
This is the simplest ASR recipe in `icefall` and can be run on CPU.
Training takes less than 30 seconds and gives you the following WER:
```
[test_set] %WER 0.42% [1 / 240, 0 ins, 1 del, 0 sub ]
```
We do provide a Colab notebook for this recipe.
[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1tIjjzaJc3IvGyKiMCDWO-TSnBgkcuN3B?usp=sharing)

### GRID

For the VSR (visual speech recognition) task, we provide two models: [Conv3d Map BiGRU CTC model][GRID_conv3d_map_bigru_ctc]
and [Conv3d ResNet18 BiGRU CTC model][GRID_conv3d_resnet18_bigru_ctc].

#### Conv3d Map BiGRU CTC Model

The WER for this model is:

||TEST|
|--|--|
|WER| 15.68% |

We provide a Colab notebook to run a pre-trained Conv3d Map BiGRU CTC model: [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1X1U2VsHD3AmRQ4UvdVEuj2y8HKJ0ZJgS?usp=sharing)

#### Conv3d ResNet18 BiGRU CTC Model

The WER for this model is:

||TEST|
|--|--|
|WER| 13.63% |

We provide a Colab notebook to run a pre-trained Conv3d ResNet18 BiGRU CTC model:  [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1PC9Fd7QcOOONFKUQqwLODwjztCuI-Oh1?usp=sharing)

For the ASR (automatic speech recognition) task, we provide one model: [Tdnn Lstm CTC model][GRID_tdnn_lstm_ctc].

#### Tdnn Lstm CTC Model

The WER for this model is:

||TEST|
|--|--|
|WER| 2.35% |

We provide a Colab notebook to run a pre-trained Tdnn Lstm CTC model: [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1bkDyVDVBhGJS5TuvjNsJ1yJ3vlCoFk9p?usp=sharing)

For the AVSR (audio-visual speech recognition) task, we provide one model: [CombineNet CTC model][GRID_combinenet_ctc].

#### CombineNet CTC Model

The WER for this model is:

||TEST|
|--|--|
|WER| 1.71% |

We provide a Colab notebook to run a pre-trained CombineNet CTC model: [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1UmCYX7GwbQ3Ms6SnoAuB8Tov46OD82hb?usp=sharing)


## Deployment with C++

Once you have trained a model in icefall, you may want to deploy it with C++,
without Python dependencies.

Please refer to the documentation
<https://icefall.readthedocs.io/en/latest/recipes/librispeech/conformer_ctc.html#deployment-with-c>
for how to do this.

We also provide a Colab notebook, showing you how to run a torch scripted model in [k2][k2] with C++.
Please see: [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1BIGLWzS36isskMXHKcqC9ysN6pspYXs_?usp=sharing)


[GRID_conv3d_map_bigru_ctc]: egs/grid/AVSR/visualnet_ctc_vsr
[GRID_conv3d_resnet18_bigru_ctc]:egs/grid/AVSR/visualnet2_ctc_vsr
[GRID_tdnn_lstm_ctc]: egs/grid/AVSR/audionet_ctc_asr
[GRID_combinenet_ctc]: egs/grid/AVSR/combinenet_ctc_avsr
[yesno]: egs/yesno/ASR
[grid]: egs/grid/AVSR
[k2]: https://github.com/k2-fsa/k2
