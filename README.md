# MyObjectDetection
android project w/ mediapipe object detection aar

https://github.com/GiadaNox/mediapipe/blob/master/mediapipe/graphs/object_detection/object_detection_mobile_gpu.pbtxt

1) 후면 카메라에서 영상을 받아오는 단계 

      FlowLimterCalculator : Incoming Data 를 변환 없이 관리

      input_video → throttled_input_video

2) Data의 크기를 물체 인식에 적합한 크기로 변환

      ImageTransformationCalculator : Input Image를 (320 x 320) 으로 변환

      throttled_input_video → transformed_input_video 

3) Data 정보를 Tensorflow 모델이 처리 가능한 형태로 변환 

      TFLiteConverterCalculator : TFLiteTensor의 형태로 input image를 변환

      transformed_input_video → image_tensor

4) Tensorflow Lite 모델을 실행하여 결과를 반환

      TFLiteInferenceCalculator : GPU 에서 Tensorflow Lite 모델(ssdlite_object_detection.tflite)을 실행하여 detection boxes, keypoints, scores 와 같은 tensor를 반환

      image_tensor → detection_tensor

5) 결과 표시에 필요한 Box Anchor 생성

      SsdAnchorsCalculator : SSD Anchor를 생성

      → anchors

6) Detection Tensor를 Decode하여 Detection Vector로 변환

      TFLiteTensorsToDetectionsCalculator : 물체 인식 결과를 물체 class 수, 인식 threshold 등의 정보에 따라 Detection Vector 형태로 변환

      detection_tensors , anchors → detections

7) 과도한 Detection 방지

      NonMaxSuppressionCalculator : Max Detection 수(3개) 등의 정보를 이용하여 한 번에 많은 물체가 인식되는 것을 방지

      detections → filtered_detections

8) Detection 결과 물체 ID를 이름으로 변환

      DetectionLabelIdToTextCalculator : Label map(ssdlite_object_detection_labelmap.txt) 을 이용하여Detection 결과의 ID를 Text와 Mapping

      filtered_detections → output_detections

9) 인식 결과 Visualize

      DetectionToRenderDataCalculator : 붉은색 박스 모양으로 Detection 결과를 표시

      output_detections → render_data

10) 인식 결과 영상으로 송출

      AnnotationOverlayCalculator : Input Image 위에 Overlay 되는 Bounding Box 그림을 그려 결과를 표시

      throttled_input_video, render_data → output_video
