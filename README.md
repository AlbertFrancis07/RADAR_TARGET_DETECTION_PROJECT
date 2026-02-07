**2D CFAR Implementation**

**Implementation Steps**
 The 2D CFAR is implemented by sliding a window  through the RDM centering a Cell UnderTest (CUT). To avoid indexing errors, the loop starts and ends with a margin determined by the number of Training and Guard cells. For every CUT, the signal levels of the surrounding Training Cells are summed. Values are converted from logarithmic (dB) to linear(power) using the db2pow function before summation to ensure mathematical accuracy.The total power is averaged over the number of training cells used. A set of Guard cells was also used at the immediate surrounding of CUT cell to prevents the target signal from leaking into training cells and raising the noise floor.


**Selection of Training, Guard Cells, and Offset**

| Parameter | Value | Logic |
| :--- | :--- | :--- |
| **Training Cells (Tr, Td)** | 10, 8 | Sufficiently large to provide a reliable estimate of the background noise environment. |
| **Guard Cells (Gr, Gd)** | 4, 4 | Prevents the target signal in the CUT from leaking into the training cells, which would artificially raise the noise floor (signal masking). |
| **Offset** | 12 | A margin added to the noise estimate to maintain a constant false alarm rate. A value of 12 was chosen to filter out transient noise spikes in this specific RDM. |

*The average power is converted back to dB using pow2db and an offset value is added to this average to establish the final threshold.

*If the signal strength of the CUT is greater than the calculated threshold, it is assigned a value of 1 (Detection); otherwise, it is suppressed to 0.

**. Suppression of Edge Cells**

*Since the sliding window requires a specific margin (Training + Guard cells) to operate, the cells at the edges of the RDM cannot be processed as a CUT.The initial loop only processes indices from Tr+Gr+1 to the inner boundaries, leaving the "outer rim" of the matrix with its original RDM values.

*To ensure a clean output, a secondary pass is performed. Any cell that falls within the margins—where the row index i < (Tr+Gr+1)  or i >(Nr/2)-(Tr+Gr) or  the column index j < (Td+Gd+1) or j > Nd-(Td+Gd) is explicitly set to 0.This results in a binary map of the same dimensions as the original RDM, where only validated detections remain.


