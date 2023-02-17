# Idea:

## Using a XGBoost Model on facial features to detect eye-gazing directions

1.  Start by collecting a dataset of labeled images, where the label is the
    direction of the gaze.

2.  Extract the features (e.g. pupil_sizes, eye_corners, eyebrow_contours, faces, and eye positions) from each image with OpenCV and use them as input to the XGBoost model.

3.  Train the XGBoost model on the labeled data, using the extracted features as input and the gaze direction as the output label.

4.  Use the feature importance analysis in XGBoost along with SHAP to identify which features are most important for the prediction, so that we know which features the model is using to make its decisions.

(Justification for why XGBoost: Since XGBoost is a powerful and efficient machine learning model that is often used for classification and is particularly effective at handling structured data, providing excellent accuracy and interpretability.)

---

## Input:

- pupil_sizes, eye_corners, eyebrow_contours, faces, and eye positions

(**Justification** for each input feature is explained in each section later)

## Output:

- 1 out of the 9 Eye-gazing directions (Up, down, left, right, up-left, up-right etc)

(**Justification**: Predefining 9 eye gazing directions allows the classification problem to be simplified and reduces the number of classes that the model has to distinguish between. This makes it easier to train the model and achieve a high accuracy. Additionally, it may be more interpretable to humans to see the prediction as one of 9 distinct classes rather than as a set of coordinates. However, using a continuous set of coordinates could be useful if the problem requires more precision or if the classification categories are not clearly defined)

---

# Procedures:

1.  Locate and extract faces in the image
2.  Extract eyes and eyebrows from the faces detected
3.  Extract eye corners from the eyes
4.  Extract pupil size from the eyes
5.  Combine all the information we have into a feature matrix
6.  Perform data preprocessing
7.  Train the model
8.  Identify and understand how each feature contributes

---

## Image Preprocessing - Removing Noise

Noise in images could be sensor noise, compression artifacts, or poor lighting conditions. Removing noise from an image can help improve its visual quality and make it more useful for further analysis.

In order to remove noise from an image, we could use spatial filters like Gaussian smoothing to blur the image and reduce high-frequency noise.

Another approach is to use thresholding or adaptive thresholding to isolate regions of the image that are most likely to contain the features of interest, while suppressing noise in other regions.

## **Justification**:

- I chose Gaussian filter because it has a smoothing effect while preserving the edges of the image, while computationally efficient

- I chose adaptive thresholding in this case as it would be a better approach for image segmentation and edge detection in cases where the lighting conditions are not consistent throughout the image, as it is capable of automatically determining the optimal threshold value for each local region of the image, based on the characteristics of the local pixel intensities

---

## Extracting Faces, Eyes, and Eyebrows

- Cascade classifier is used to identify faces and eyes by computing Haar-like features, as it is fast and effective

- Once the face and eye regions have been identified, a region of interest (ROI) can be defined above each eye to isolate the area where the eyebrows are most likely to be found.

- In the code, I used color analysis to identify the specific regions within the ROI that correspond to the eyebrows and extract pixels with specific color values or by analyzing color histograms within the ROI.

### Justification of extracting eyebrows:

Position of the eyebrows relative to the eyes can provide information about the direction of the gaze. When a person looks up or down, their eyebrows move accordingly, and these changes can be detected by tracking the position of the eyebrows.

### Improvements I could make:
Extracted eyebrow regions can be further processed using techniques such as edge detection or morphological operations to improve their shape and position accuracy.

---

## Extracting eye corners

Reason to extract eye corners is to measure the position and movement of the eyes as eye tracking relies on detecting eye landmarks such as the corners to determine where a person is looking and how their gaze is moving.

---

## Extracting pupil size

Reason to extract the size of the pupil is that it does changes in response to changes in lighting and focus. We can use contour detection or Hough circle detection to detect and extract pupil

1.  Detect the contour of the pupil.
2.  Find the contour with the maximum area, as it is likely to be the pupil

---
