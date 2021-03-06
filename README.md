# Lip2Wav

*Generate high quality speech from only lip movements*. This code is part of the paper: _Learning Individual Speaking Styles for Accurate Lip to Speech Synthesis_ published at CVPR'20.

[[Paper]](https://arxiv.org/abs/2005.08209) | [[Project Page]](http://cvit.iiit.ac.in/research/projects/cvit-projects/speaking-by-observing-lip-movements) | [[Demo Video]](https://www.youtube.com/watch?v=HziA-jmlk_4)
 <p align="center">
  <img src="images/banner.gif"/></p>

----------
Recent Updates
----------
- Dataset and Pretrained model for "Chemistry lectures" speaker is released!
- More speakers from the Lip2Wav dataset and their models coming soon! Stay tuned!

----------
Highlights
----------
 - First work to generate intelligible speech from only lip movements in unconstrained settings.
 - Sequence-to-Sequence modelling of the problem.
 - Dataset for 5 speakers containing 100+ hrs of video data made available! [[Dataset folder of this repo]](https://github.com/Rudrabha/Lip2Wav/tree/master/Dataset) 
 - Complete training code and pretrained models made available.
 - Inference code to generate results from the pre-trained models.
 - Code to calculate metrics reported in the paper is also made available.


Prerequisites
-------------
- `Python 3.7.4` (code has been tested with this version)
- ffmpeg: `sudo apt-get install ffmpeg`
- Install necessary packages using `pip install -r requirements.txt`
- Face detection [pre-trained model](https://www.adrianbulat.com/downloads/python-fan/s3fd-619a316812.pth) should be downloaded to `face_detection/detection/sfd/s3fd.pth`

Getting the weights
----------
| Speaker  | Link to the model |
| ------------- | :---------------: |
| Chemistry  | [Link](https://iiitaphyd-my.sharepoint.com/:f:/g/personal/radrabha_m_research_iiit_ac_in/Eo6wv0MH0YlGn5sgRaPnEkIBUf910oTDNnAf4X7XTqmvFQ?e=u8bhPR)  |


Downloading the dataset
----------

<!--If you would like to train/test on our Lip2Wav dataset, download it from our [project page](http://cvit.iiit.ac.in/research/projects/cvit-projects/speaking-by-observing-lip-movements). The download will be a small zip file with several `.csv` files containing the YouTube IDs of the videos to create the dataset for each speaker. Assuming the zip file is extracted as follows:-->
The dataset is present in the Dataset folder in this repository. The folder `Dataset/chem` contains `.txt` files for the train, val and test sets.

```
data_root (Lip2Wav in the below examples)
├── Dataset
|	├── agad, chem, hs (list of speaker-specific folders)
|	|    ├── train.txt, test.txt, val.txt (each will contain YouTube IDs to download)
```

To download the complete video data for a specific speaker, just run:

```bash
sh download_speaker.sh Dataset/chem
```

This should create

```
Dataset
├── chem (or any other speaker-specific folder)
|	├── train.txt, test.txt, val.txt
|	├── videos/		(will contain the full videos)
|	├── intervals/	(cropped 30s segments of all the videos) 
```


Preprocessing the dataset
----------
```bash
python preprocess.py --speaker_root Dataset/chem --speaker chem
```

Additional options like `batch_size` and number of GPUs to use can also be set.


Generating for the given test split
----------
```bash
python complete_test_generate.py -d Dataset/chem -r Dataset/chem/test_results \
--preset synthesizer/prests/chem.json --checkpoint <path_to_checkpoint>

#A sample checkpoint_path  can be found in hparams.py alongside the "eval_ckpt" param.
```

This will create:
```
Dataset/chem/test_results
├── gts/  (cropped ground-truth audio files)
|	├── *.wav
├── wavs/ (generated audio files)
|	├── *.wav
```

Calculating the metrics
----------
You can calculate the `PESQ`, `ESTOI` and `STOI` scores for the above generated results using `score.py`:
```bash
python score.py -r Dataset/chem/test_results
```

Training
----------
```bash
python train.py <name_of_run> --data_root Dataset/chem/ --preset synthesizer/prests/chem.json
```
Additional arguments can also be set or passed through `--hparams`, for details: `python train.py -h`


License and Citation
----------
The software is licensed under the MIT License. Please cite the following paper if you have use this code:
```
@article{Prajwal2020LearningIS,
  title={Learning Individual Speaking Styles for Accurate Lip to Speech Synthesis},
  author={K R Prajwal and Rudrabha Mukhopadhyay and Vinay Namboodiri and C. V. Jawahar},
  journal={ArXiv},
  year={2020},
  volume={abs/2005.08209}
}
```


Acknowledgements
----------
The repository is modified from this [TTS repository](https://github.com/CorentinJ/Real-Time-Voice-Cloning). We thank the author for this wonderful code. The code for Face Detection has been taken from the [face_alignment](https://github.com/1adrianb/face-alignment) repository. We thank the authors for releasing their code and models.
