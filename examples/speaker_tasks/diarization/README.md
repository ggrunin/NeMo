# Speaker Dirarzation

Documentation section for speaker related tasks can be found at:
 - [Speaker Diarization](https://docs.nvidia.com/deeplearning/nemo/user-guide/docs/en/main/asr/speaker_diarization/intro.html)
 - [Speaker Identification and Verification](https://docs.nvidia.com/deeplearning/nemo/user-guide/docs/en/main/asr/speaker_recognition/intro.html)


## Features of NeMo Speaker Diarization
- Provides pretrained speaker embedding extractor models and VAD models.
- Does not need to be tuned on dev-set while showing the better performance than AHC+PLDA method in general.
- Estimates the number of speakers in the given session.
- Provides example script for asr transcription with speaker labels. 

## Supported Pretrained Speaker Embedding Extractor models
- [titanet_large](https://catalog.ngc.nvidia.com/orgs/nvidia/teams/nemo/models/titanet_large)
- [ecapa_tdnn](https://ngc.nvidia.com/catalog/models/nvidia:nemo:ecapa_tdnn)
- [speakerverification_speakernet](https://catalog.ngc.nvidia.com/orgs/nvidia/teams/nemo/models/speakerverification_speakernet)

## Supported Pretrained VAD models
- [vad_marblenet](https://catalog.ngc.nvidia.com/orgs/nvidia/teams/nemo/models/vad_marblenet)
- [vad_telephony_marblenet](https://catalog.ngc.nvidia.com/orgs/nvidia/teams/nemo/models/vad_telephony_marblenet)

## Supported ASR models
QuartzNet, CitriNet and Conformer-CTC models are supported. 
Recommended models on NGC:
- [stt_en_quartznet15x5](https://catalog.ngc.nvidia.com/orgs/nvidia/teams/nemo/models/stt_en_quartznet15x5)
- [stt_en_conformer_ctc_large](https://catalog.ngc.nvidia.com/orgs/nvidia/teams/nemo/models/stt_en_conformer_ctc_large)
- [stt_en_citrinet_1024](https://catalog.ngc.nvidia.com/orgs/nvidia/teams/nemo/models/stt_en_citrinet_1024)

## Performance
Diarization Error Rate (DER) table of `titanet_large.nemo` model on well known evaluation datasets. 

|         Evaluation<br>Condition     | NIST SRE 2000 | AMI<br>(Lapel) | AMI<br>(MixHeadset) | CH109 |
|:-----------------------------------:|:-------------:|:--------------:|:-------------------:|:-----:|
|  Oracle VAD <br>KNOWN # of Speakers  |      6.73     |      2.03      |         1.73        |  1.19 |
| Oracle VAD<br> UNKNOWN # of Speakers |     5.38     |      2.03      |         1.89        |  1.63 |

* All models were tested using embedding extractor with window size 1.5s and shift length 0.75s
* The above result is based on the oracle Voice Activity Detection (VAD) result.
* This result is based on [titanet_large.nemo](https://catalog.ngc.nvidia.com/orgs/nvidia/teams/nemo/models/titanet_large) model.

<br/>

## Run Speaker Diarization on Your Audio Files

#### Example script
```bash
  python offline_diarization.py \
    diarizer.manifest_filepath=<path to manifest file> \
    diarizer.out_dir='demo_output' \
    diarizer.speaker_embeddings.model_path=<pretrained modelname or path to .nemo> \
    diarizer.vad.model_path=<pretrained modelname or path to .nemo>
```

If you have oracle VAD files and groundtruth RTTM files for evaluation:
Provide rttm files in the input manifest file and enable oracle_vad as shown below. 

```bash
python offline_diarization.py \
  python speaker_diarize.py \
    diarizer.manifest_filepath=<path to manifest file> \
    diarizer.out_dir='demo_output' \
    diarizer.speaker_embeddings.model_path=<pretrained modelname or path to .nemo> \
    diarizer.oracle_vad=True
```

#### Arguments
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; To run speaker diarization on your audio recordings, you need to prepare the following file.

- **`diarizer.manifest_filepath`: <manifest file>** Path to manifest file 

Example: `manifest.json`

```bash
{"audio_filepath": "/path/to/audio_file", "offset": 0, "duration": null, label: "infer", "text": "-", "num_speakers": null, "rttm_filepath": "/path/to/rttm/file", "uem_filepath"="/path/to/uem/filepath"}
```
Mandatory fields are `audio_filepath`, `offset`, `duration`, `label:"infer"` and `text: <ground truth or "-" >`  , and the rest are optional keys which can be passed based on the type of evaluation 

Some of important options in config file: 

- **`diarizer.speaker_embeddings.model_path`: speaker embedding model name**

Specify the name of speaker embedding model, then the script will download the model from NGC. Currently, we support 'titanet_large', 'ecapa_tdnn' and 'speakerverification_speakernet'.

`diarizer.speaker_embeddings.model_path='titanet_large'`

You could also download *.nemo files from [this link](https://ngc.nvidia.com/catalog/models?orderBy=scoreDESC&pageNumber=0&query=SpeakerNet&quickFilter=&filters=) and specify the full path name to the speaker embedding model file (`*.nemo`).

`diarizer.speaker_embeddings.model_path='path/to/titanet_large.nemo'` 
 
- **`diarizer.vad.model_path`: voice activity detection modle name or path to the model**

Specify the name of VAD model, then the script will download the model from NGC. Currently, we have 'vad_marblenet' and  'vad_telephony_marblenet' as options for VAD models.

`diarizer.vad.model_path='vad_telephony_marblenet'`


Instead, you can also download the model from [vad_marblenet](https://ngc.nvidia.com/catalog/models/nvidia:nemo:vad_marblenet) and [vad_telephony_marblenet](https://ngc.nvidia.com/catalog/models/nvidia:nemo:vad_telephony_marblenet) and specify the full path name to the model as below.

`diarizer.vad.model_path='path/to/vad_telephony_marblenet.nemo'`

- **`diarizer.speaker_embeddings.parameters.multiscale_weights`: multiscale diarization (Experimental)**

Multiscale diarization system employs multiple scales at the same time to obtain a finer temporal resolution. To use multiscale feature, at least two scales and scale weights should be provided. The scales should be provided in descending order, from the longest scale to the base scale (the shortest). If multiple scales are provided, multiscale_weights must be provided in list format. The following example shows how multiscale parameters are specified and the recommended parameters.

#### Example script: single-scale and multiscale
Single-scale setting:
```bash
  python offline_diarization.py \
     ... <other paramerters> ...
     parameters.window_length_in_sec=1.5 \
     parameters.shift_length_in_sec=0.75 \
     parameters.multiscale_weights=null \
```

Multiscale setting (base scale - window_length 0.5 s and shift_length 0.25):
```bash
  python offline_diarization.py \
     ... <other paramerters> ...
     parameters.window_length_in_sec=[1.5,1.0,0.5] \
     parameters.shift_length_in_sec=[0.75,0.5,0.25] \
     parameters.multiscale_weights=[0.33,0.33,0.33] \
```
 
<br/>

## Run Speech Recognition with Speaker Diarization

Using the script `offline_diarization_with_asr.py`, you can transcribe your audio recording with speaker labels as shown below:

```
[00:03.34 - 00:04.46] speaker_0: back from the gym oh good how's it going 
[00:04.46 - 00:09.96] speaker_1: oh pretty well it was really crowded today yeah i kind of assumed everylonewould be at the shore uhhuh
[00:12.10 - 00:13.97] speaker_0: well it's the middle of the week or whatever so
```

Currently, offline_diarization_with_asr supports QuartzNet English model and ConformerCTC model (`QuartzNet15x5Base-En`, `stt_en_conformer_ctc_large`). 

#### Example script

```bash
python offline_diarization_with_asr.py \
    diarizer.manifest_filepath=<path to manifest file> \
    diarizer.out_dir='demo_asr_output' \
    diarizer.speaker_embeddings.model_path=<pretrained modelname or path to .nemo> \
    diarizer.asr.model_path=<pretrained modelname or path to .nemo> \
    diarizer.asr.parameters.asr_based_vad=True
```
If you have reference rttm files or oracle number of speaker information, you can provide those file paths and number of speakers in the manifest file path and pass `diarizer.clustering.parameters.oracle_num_speakers=True` as shown in the following example.

```bash
python offline_diarization_with_asr.py \
    diarizer.manifest_filepath=<path to manifest file> \
    diarizer.out_dir='demo_asr_output' \
    diarizer.speaker_embeddings.model_path=<pretrained modelname or path to .nemo> \
    diarizer.asr.model_path=<pretrained modelname or path to .nemo> \
    diarizer.asr.parameters.asr_based_vad=True \
    diarizer.clustering.parameters.oracle_num_speakers=True
```

#### Output folders

The above script will create a folder named `./demo_asr_output/`.
In `./demo_asr_output/`, you can check the results as below.

```bash
./asr_with_diar
├── pred_rttms
    └── my_audio1.json
    └── my_audio1.txt
    └── my_audio1.rttm
    └── my_audio1_gecko.json
│
└── ...
```

`*.json` files contains word-by-word json output with speaker label and time stamps. We also provide json output file for [gecko](https://gong-io.github.io/gecko/) tool, where you can visualize the diarization result along with ASR output.

Example: `./demo_asr_output/pred_rttms/my_audio1.json`
```bash
{
    "status": "Success",
    "session_id": "my_audio1",
    "transcription": "back from the gym oh good ...",
    "speaker_count": 2,
    "words": [
        {
            "word": "back",
            "start_time": 0.44,
            "end_time": 0.56,
            "speaker_label": "speaker_0"
        },
...
        {
            "word": "oh",
            "start_time": 1.74,
            "end_time": 1.88,
            "speaker_label": "speaker_1"
        },
        {
            "word": "good",
            "start_time": 2.08,
            "end_time": 3.28,
            "speaker_label": "speaker_1"
        },
```

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `*.txt` files contain transcription with speaker labels and corresponding time.

Example: `./demo_asr_output/pred_rttms/my_audio1.txt`
```
[00:03.34 - 00:04.46] speaker_0: back from the gym oh good how's it going
[00:04.46 - 00:09.96] speaker_1: pretty well it was really crowded today yeah i kind of assumed everylonewould be at the shore uhhuh
[00:12.10 - 00:13.97] speaker_0: well it's the middle of the week or whatever so
[00:13.97 - 00:15.78] speaker_1: but it's the fourth of july mm
[00:16.90 - 00:21.80] speaker_0: so yeahg people still work tomorrow do you have to work tomorrow did you drive off yesterday
```
 
### Optional Features for Speech Recognition with Speaker Diarization
 
#### Beam Search Decoder

Beam-search decoder can be applied to CTC based ASR models. To use this feature, [pyctcdecode](https://github.com/kensho-technologies/pyctcdecode) should be installed. [pyctcdecode](https://github.com/kensho-technologies/pyctcdecode) supports word timestamp generation and can be applied to speaker diarization. pyctcdecode also requires [KenLM](https://github.com/kpu/kenlm) and KenLM is recommended to be installed using PyPI. Install pyctcdecode in your environment with the following commands: 
```
pip install pyctcdecode
pip install https://github.com/kpu/kenlm/archive/master.zip
```
You should provide a trained KenLM language model to use pyctcdecode. Binary or `.arpa` format can be provided to hydra configuration as below.

```bash
  python offline_diarization_with_asr.py \
    ... <other paramerters> ...
    diarizer.asr.ctc_decoder_parameters.pretrained_language_model="/path/to/kenlm_language_model.binary"
```
You can download publicly available language models (`.arpa` files) at [KALDI Tedlium Language Models](https://kaldi-asr.org/models/m5). Download [4-gram Big ARPA](https://kaldi-asr.org/models/5/4gram_big.arpa.gz) and provide the model path.
 
The following CTC decoder parameters can be modified to optimize the performance.      
`diarizer.asr.ctc_decoder_parameters.beam_width` (default: 32)      
`diarizer.asr.ctc_decoder_parameters.alpha` (default: 0.5)       
`diarizer.asr.ctc_decoder_parameters.beta` (default: 2.5)       
 
#### Realign Words with a Language Model (Experimental)

Diarization result with ASR transcript can be enhanced by applying a language model. To use this feature, python package [arpa](https://pypi.org/project/arpa/) should be installed.
```
pip install arpa
```
`diarizer.asr.realigning_lm_parameters.logprob_diff_threshold` can be modified to optimize the diarization performance (default value is 1.2). The lower the threshold, the more changes are expected to be seen in the output transcript.   

`arpa` package also uses KenLM language models as in pyctcdecode. You can download publicly available [4-gram Big ARPA](https://kaldi-asr.org/models/5/4gram_big.arpa.gz) model and provide the model path to hydra configuration as follows.
 

```bash
python offline_diarization_with_asr.py \
    ... <other paramerters> ...
    diarizer.asr.realigning_lm_parameters.logprob_diff_threshold=1.2 \
    diarizer.asr.realigning_lm_parameters.arpa_language_model="/path/to/4gram_big.arpa"\
```
