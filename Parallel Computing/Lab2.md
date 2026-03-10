
Sliding window for image processing

- Recalculate each pixel based on filter
- Similar to convolution

The filter we use is a weighted averaging

![[Pasted image 20260211131041.png]]

- Apply filter on N by N matrix
- mask operations distributed evenly
- Use scatterv to distribute initial image and gatherv to collect processed matrix
- The ones at edges don't get processed (ignore first and last row and column)
- Matrix A is initialized synthetically using rand() with seed of 1 (limit to numbers between 0 and 255)