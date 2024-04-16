

# NIST FRVT 1:N Implementation
This package is the implementation of 1:N FRVT face detection and recognition algorithms, and generates the submission packages that would be sent to NIST for evaluation

## Overview
This is a C++ based implementation for face detection and recognition for FRVT 1:N by NIST. The pipeline first parses FRVT::Image to OpenCV's format cv::Mat to be compatible with OpenCV's APIs. Then the images are fed to a neural network based face detection, then each detected face gets passed to a feature extraction - another neural network - module to generate face feature vector of embeddings. More info is illustrated in the following diagram:

![Implementation Block Diagram](readme_doc/afrengineFRVT1NImpl.drawio.png).

## Installation and Submission Package Creation
**IMPORTANT NOTICE:** \
This application is intended to run on the official Ubuntu image provided by NIST which can be downloaded from [https://nigos.nist.gov/evaluations/ubuntu-20.04.3-live-server-amd64.iso](https://nigos.nist.gov/evaluations/ubuntu-20.04.3-live-server-amd64.iso).

First you need to clone the main FRVT repository from NIST

    git clone https://github.com/usnistgov/frvt.git


Navigate into the repository and install the packages that will be installed on NIST's test harness

    cd <YOUR_REPO_LOCATION>
    chmod +x common/scripts/*
    sudo apt update
    ./common/scripts/install_packages.sh

Now remove 1N folder from NIST's repo to be replaced by this repo

    sudo rm -r 1N

and clone this repo

    git clone https://github.com/MichaelHannalla/frvt1n
  
then rename the repo to abide by NIST's folder naming conventions
 

    sudo mv frvt1n 1N

Navigate into 1N folder (our implementation)

    cd 1N/
   
Allow the necessary scripts to run as executables

    chmod +x scripts/*
    chmod +x run_validate_1N.sh

Then call the building and validation against test harness, this step will generate the submission package that will be sent to NIST

    ./scripts/build_afrengine_impl.sh && ./run_validate_1N.sh

If you want the original readme from NIST regarding the 1N package, you can check [README_NIST.md](https://github.com/MichaelHannalla/frvt1n/blob/master/README_NIST.md)  file in this repo

## Configuration
The implementation is configurable in the sense that you don't need to re-compile or build the source code to change some behaviors of the algorithm.
To edit the configuration (you need elevated privileges)

    sudo nano 1N/config/afrengine_config.txt

You'll find parameters to be configured as follows:
**IMPORTANT NOTE:** Do not change the names of the parameters and use the correct datatype for each parameter to avoid unpredictable behavior.

| Parameter | Description | Default Value
| ------- | -------------- | ----------- |
| `CFG_FACE_DETECTOR`  | Algorithm used for face detection, currently available is one from [`yunet`] | `yunet` (string)
| `CFG_FACE_DETECTOR_MODEL_PATH`  | Path for the model file of the face detector (relative to config directory) | `yunet/face_detection_yunet_2022mar.onnx` (string)
| `CFG_FACE_DETECTOR_SCORE_THRESH`  | Score threshold of face detector (confidence for each face) | `0.7` (numeric)
| `CFG_FACE_DETECTOR_IOU_THRESH`  | IOU threshold of face detector (overlap of bounding boxes) | `0.1` (numeric)
| `CFG_FACE_FEATURE_EXTRACTOR`  | Algorithm used for face feature extraction, currently available is one from [`facenet`] | `facenet` (string)
| `CFG_FACE_FEATURE_EXTRACTOR_MODEL_PATH`  | Path for the model file of the face feature extractor| `facenet/graph_final.pb` (relative to config directory)
| `CFG_COSINE_SIMILARITY_THRESH`  | Cosine similarity threshold between face embedding vectors above which the templates will be considered to be belonging to the same person| `0.3` (numeric)


## License and Maintenance
Any unauthorized distribution of this software is prohibited.

If you observe any bugs or unexpected behavior, please send an email to: michael.sami.hanna@gmail.com 
