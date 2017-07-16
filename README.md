Please find the whole code repo and models at [CDC-bitbucket](https://bitbucket.org/columbiadvmm/cdc)

Project website: [http://www.ee.columbia.edu/ln/dvmm/researchProjects/cdc/](http://www.ee.columbia.edu/ln/dvmm/researchProjects/cdc/)

# CDC: Convolutional-De-Convolutional Networks for Precise Temporal Action Localization in Untrimmed Videos

By Zheng Shou, Jonathan Chan, Alireza Zareian, Kazuyuki Miyazawa, and Shih-Fu Chang.

### Introduction:

CDC is a deep learning framework for per-frame labeling and precise temporal action localization in untrimmed long videos.

This code has been tested on Ubuntu 14.04 with a single NVIDIA GeForce GTX TITAN X card.

Please use "Issues" on github instead of bitbucket to ask questions or report bugs. Thanks.

[comment]: # ()
Current code is our rough version and we are improving its implementation details, while the current version suffices to run demo, repeat our experimental results, and train your own models.

### License

CDC is released under the MIT License (refer to the LICENSE file for details).

### Citing:

If you find CDC useful, please consider citing:

    @inproceedings{cdc_shou_cvpr17,
      author = {Zheng Shou and Jonathan Chan and Alireza Zareian and Kazuyuki Miyazawa and Shih-Fu Chang},
      title = {CDC: Convolutional-De-Convolutional Networks for Precise Temporal Action Localization in Untrimmed Videos},
      year = {2017},
      booktitle = {CVPR} 
      }
    
We build this repo based on C3D and THUMOS Challenge 2014 . Please cite the following papers as well:

D. Tran, L. Bourdev, R. Fergus, L. Torresani, and M. Paluri, Learning Spatiotemporal Features with 3D Convolutional Networks, ICCV 2015.

Y. Jia, E. Shelhamer, J. Donahue, S. Karayev, J. Long, R. Girshick, S. Guadarrama, and T. Darrell, Caffe: Convolutional Architecture for Fast Feature Embedding, arXiv 2014.

A. Karpathy, G. Toderici, S. Shetty, T. Leung, R. Sukthankar, and L. Fei-Fei, Large-scale Video Classification with Convolutional Neural Networks, CVPR 2014.

    @misc{THUMOS14,
      author = "Jiang, Y.-G. and Liu, J. and Roshan Zamir, A. and Toderici, G. and Laptev, I. and Shah, M. and Sukthankar, R.",
      title = "{THUMOS} Challenge: Action Recognition with a Large Number of Classes",
      howpublished = "\url{http://crcv.ucf.edu/THUMOS14/}",
      Year = {2014}
      }
      
    @inproceedings{scnn_shou_wang_chang_cvpr16,
      author = {Zheng Shou and Dongang Wang and Shih-Fu Chang},
      title = {Temporal Action Localization in Untrimmed Videos via Multi-stage CNNs},
      year = {2016},
      booktitle = {CVPR} 
      }
      
### Installation:
- Hint: please refer to [C3D-v1.0](https://github.com/facebook/C3D/tree/master/C3D-v1.0) and [Caffe](https://github.com/BVLC/caffe) for more details about compilation such as making your own Makefile.config
- Compile CDC: `cd ./CDC/; make all`
- For the sake of using our code smoothly, please first get familiar with [C3D](https://github.com/facebook/C3D).

### Run demo:
- This demo is designed to let users to have a quick try of CDC feature extraction.
- More details of this demo:
1. we provide input data in `demo/data/window` along with input data list file `demo/data/test.lst`
2. each input data sample is a 32-frames long window. In order to directly reuse `VIDEO_SEGMENTATION_DATA` data format developed in [C3D-v1.0](https://github.com/facebook/C3D/tree/master/C3D-v1.0), each of our input data is stored in bin format and consists of pixel values stacked over time (in the channel dim, besides RGB values, the pixel-level ground truth label is attached as the 4-th value; all pixels in the same frame have the same label; during testing, we only need provide random value for the label since it won't be used). We provide an example code for generating such bin file on THUMOS test set in the next section.
3. run the demo: `cd demo; ./xfeat.sh;`
4. output results will be stored in `demo/feat`

### Reproduce results on THUMOS 2014 dataset:
- Pre-process
1. first extract all frames in the following folder which will be used in the next step python file: `inputdir = '/DATA_ROOT/THUMOS14/test/all_frames_pervideo/'`
2. `cd THUMOS14/predata/test` and run `python gen_test_bin_and_list.py` to generate the bin files and the list file for the test set.

- CDC network prediction
1. `cd THUMOS14/test` and you will see needed files for using CDC network to do prediction (i.e. feature extraction of the last layer) and outputs will be stored in `feat`
2. the trained model used for feature extraction is `/CDC_root/model/thumos_CDC/convdeconv-TH14_iter_24390`
3. the last layer of our trained model has 22 nodes corresponding to 22 possible frame-level classes(from the first to the last: background, action1-20, ambiguous)

- Post-process
1. cd `THUMOS14/test/postprocess` and we have three post-processing steps in matlab
2. run `matlab step1_gen_test_metadata.m` and will generate `metadata.mat` which consists of three vectors (each vector corresponds to each video's all frames ordered in time for all test videos):
    - frmid: frame id in each video, starts with 1
    - videoid: belongs to which video
    - kept_frm_index: when we generate bin window files, the last window may overlap with its previous window and thus have duplicate frames; this kept_frm_index can be used to index all unique frames from the frame list for bin window files.
3. run `matlab step2_read_feat.m` and will read all caffe outputs into two matlab matrixs:
    - read_res.mat: this contains `prob`, which is the prediction results directly read from CDC outputs. #frames by #classes.
    - proball.mat: this contains `proball`, which uses kept_frm_index to remove redundant frames in the above `prob` and removes confidence score prediction for the last class (ambiguous)
4. run `matlab step3_gen_CDC_det.m` to produce action segment instances prediction for temporal localization.
    - `SCNN-proposal.mat` is the proposal results of [Segment-CNN](https://github.com/zhengshou/scnn/) and we follow Segment-CNN to keep segments with confidence score > 0.7
    - `res_seg_swin.mat` is the results of refining temporal boundaries of segment candidates from Segment-CNN. This mat file contains `seg_swin`: each row stands for one candidate segment; as for each column: 
        * 1: video name in THUMOS14 test set
        * 2: sliding window length measured by number of frames
        * 3: start frame index
        * 4: end frame index
        * 5: start time
        * 6: end time
        * 9: confidence score of being the class indicated in the column 11
        * 10: confidence score of being action/non-background
        * 11: the predicted action class (from the 20 action classes [index 1-20] and the background [index 0])

- Evaluation
1. Per-frame labeling:
    - note that `multi-label-test.mat` contains ground truth label for each frame. #frames by #classes.
    - `cd THUMOS14/eval/PreFrameLabeling/` and run `matlab compute_framelevel_mAP.m`; map is the per-frame labeling mAP

2. Temporal localization:
    - `cd THUMOS14/eval/TemporalActionLocalization/` and run `matlab eval_thumos14.m`
    - results are stored in `res_CDC_thumos14.mat`. we vary the overlap threshold IoU used in evaluation from 0.3 to 0.7

### Train your own model:
- Prepare pre-trained model as init: as explained in the paper, we use weights in sports1m model (`model/sports1m_C3D/conv3d_deepnetA_sport1m_iter_1900000`) to init our CDC network. We prepare this following script for generating the init model of CDC: `cd THUMOS14/training/init/; ./run_net_surgey_sports1m_convdeconv.sh` and it generates `conv3d_deepnetA_sport1m_iter_1900000.convdeconv` to be used as init during fine-tuning your own model.

- Training example: example files for setting up caffe to run CDC fine-tuning are in `THUMOS14/training/`. Please refer to the above code for THUMOS test set window bin files generation to prepare your own training data. And please refer to [C3D](https://github.com/facebook/C3D) and [Caffe](https://github.com/BVLC/caffe) for more general instructions about how to train 3D CNN model.



