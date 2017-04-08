To be updated

# CDC

By Zheng Shou, Jonathan Chan, Alireza Zareian, Kazuyuki Miyazawa, and Shih-Fu Chang.

### Introduction:

Convolutional-De-Convolutional Networks (CDC) is a deep learning framework for per-frame labeling and precise temporal action localization in untrimmed long videos.

This code has been tested on Ubuntu 14.04 with a single NVIDIA GeForce GTX TITAN X card.

Please use "GitHub Issues" to ask questions or report bugs. Thanks.

[comment]: # (Current code is our rough version and we are improving its implementation details, while the current version suffices to run demo, repeat our experimental results, and train your own models.)

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
1. Compile CDC:
    - Hint: please refer to [C3D-v1.0](https://github.com/facebook/C3D/tree/master/C3D-v1.0) and [Caffe](https://github.com/BVLC/caffe) for more details about compilation such as making your own Makefile.config
    - `cd ./CDC/; make all`


