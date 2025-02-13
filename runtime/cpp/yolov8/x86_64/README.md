**Last HailoRT version checked - 4.15.0**

**Disclaimer:** <br />
This code example is provided by Hailo solely on an “AS IS” basis and “with all faults”. No responsibility or liability is accepted or shall be imposed upon Hailo regarding the accuracy, merchantability, completeness or suitability of the code example. Hailo shall not have any liability or responsibility for errors or omissions in, or any business decisions made by you in reliance on this code example or any part of it. If an error occurs when running this example, please open a ticket in the "Issues" tab.<br />
Please note that this example was tested on specific versions and we can only guarantee the expected results using the exact version mentioned above on the exact environment. The example might work for other versions, other environment or other HEF file, but there is no guarantee that it will.


This is a hailort C++ API yolov8 detection example.

The example does the following:

1. Creates a device (pcie)
2. Reads the network configuration from a yolov8 HEF file
3. Prepares the application for inference
4. Runs inference and postprocess (using xtensor library) on a given image\video file or a camera 
5. Draws the detection boxes on the original image\video\camera input
6. Prints the object detected + confidence to the screen
5. Prints statistics

**NOTE**: Currently support only devices connected on a PCIe link.

**NOTE**: This example was tested with a yolov8s & a yolov8m model.


Prequisites:

OpenCV 4.2.X

CMake >= 3.20

HailoRT >= 4.10.0

Xtensor - no installation or build needed as it's complied from the web as an external project.


**NOTE**: You need to set the BASE_DIR variable in the CMakeLists.txt to be the folder path to the location of the yolov8_cpp folder.

To compile the example run `./build.sh`

To run the compiled example:

For an image:
`./build/x86_64/vstream_yolov8_example_cpp -hef=YOLOv8_HEF_FILE.hef -input=IMAGE_FILE.jpg`
For a video:
`./build/x86_64/vstream_yolov8_example_cpp -hef=YOLOv8_HEF_FILE.hef -input=VIDEO_FILE.mp4`
For a camera input:
`./build/x86_64/vstream_yolov8_example_cpp -hef=YOLOv8_HEF_FILE.hef -input=`

Example:
`./build/x86_64/vstream_yolov8_example_cpp -hef=yolov8s.hef -input=full_mov_slow.mp4`


**NOTE**: This example uses xtensor C++ ibrary compiled from the xtl git as an external source. 

**NOTE**: You can also save the processed image\video by commenting in a few lines in the "post_processing_all" function - for images, the cv::imwrite line and for a video the other commented out lines.

**NOTE**: There should be no spaces between "=" given in the command line arguments and the file name itself.

**NOTE**: You can play with the values of IOU_THRESHOLD and SCORE_THRESHOLD in the yolov8_postprocess.cpp file for different videos to get more detections.

**NOTE**: In case you prefer to perform the Sigmoid on host, you can comment in the relevant line to do that. Please notice that you'll need a HEF file that does not have an on-chip sigmoid if you choose to use the example in such a way. 

**NOTE**: The example was built for yolov8 model trained on COCO dataset with 80 classes. From random testing, the example works with yolov8 models that are trained on less classes, but for it to work a change is to be made in `yolov8_postprocess.cpp` at line 26, changing `#define NUM_CLASSES 80` to `#define NUM_CLASSES X` where `X` is the number of classes the model was trained on.

**NOTE**: The pre-compiled Yolov8 HEF files in the Hailo Model Zoo are compiled to 8-bit. Hailo supply the option to compile the model with 16-bit output layers for those who desire it.
Both scores and data dequantization is done manually in the postprocessing functions. 
This means that you will not get good detection (or detections at all) with a Yolov8 with 16-bit output layers. 
If you choose to work with your own HEF that is with a 16-bit output, you need to change the code from **uint8_t** to **uint16_t** in the following lines:

double_buffer.hpp - lines 32, 43, 61, 69, 97

yolov8_inference.cpp - line 68

yolov8_postprocess.cpp - lines 78, 82, 139 

hailo_tensors.hpp - lines 19, 29, 46, 85

tensors.hpp - lines 24, 27, 43

