**# Aerial-GCP-Pose-Estimation
By: Christopher Saldanha**

**NOTE**: 15 epoch's have been utilized. This may take a longer time to train depending on your hardware. In case of time efficiency kindly reduce it to 5.


**1. Network Architecture**

I implemented a _Multi-Task Learning (MTL) Architecture using a ResNet-50 backbone._

_Backbone_: Pre-trained ResNet-50 was used for robust feature extraction from high-resolution aerial imagery. The final fully connected layer was replaced with an Identity layer to extract raw spatial features.

_Regression Head:_ A bifurcated branch consisting of a Linear(256) -> ReLU -> Linear(2) structure. A Sigmoid activation was applied at the output to ensure predicted coordinates are bounded within the normalized [0, 1] range.

_Classification Head:_ A parallel branch with a Linear(256) -> ReLU -> Dropout(0.3) -> Linear(3) structure to classify the GCP marker shape (Cross, Square, L-Shaped).








**2. Training Strategy**

_Data Augmentation:_ Used the albumentations library to resize images to 512x512. This library was chosen because it automatically recalculates keypoint coordinates during resizing.

_Coordinate Normalization:_ Coordinates were converted to percentages of image width/height. This prevents exploding gradients and makes the model resolution-agnostic.

_Loss Function:  Regression:_ Mean Squared Error (MSE)

_Classification:_ Cross-Entropy Loss

_Balanced Loss:_ Total Loss = (10.0 * MSE) + CrossEntropy. The multiplier was added to balance the tiny magnitude of normalized coordinate errors with the larger classification errors.








**3. Data Handling & Challenges (Key Insights)**

_Dataset Anomalies:_ During Exploratory Data Analysis (EDA), I discovered that the gcp_marks.json file was heavily un-sanitized.

_Incomplete Annotations:_ Approximately 49.5% (495 out of 1000) of the training records were missing the verified_shape label.

_Mitigation:_ I implemented a robust data sanitation filter in the PyTorch Dataset constructor. This proactive filter identifies and skips any record missing required keys before training begins, preventing KeyError crashes in the DataLoader and ensuring the model only learns from high-quality, verified data.









**4. Instructions to Reproduce**

Mount the Google Drive dataset shortcut.

Run the provided Google Colab notebook cells in order.

The script will automatically clean the data, train for 15 epochs, and output _predictions.json.
_

5: Download Best Model Weight

Click on this link in order to Download the Best Model Weight : https://drive.google.com/file/d/1O2aDh6gdibETO9ayok8gwfRT6nT4ypL2/view?usp=sharing
