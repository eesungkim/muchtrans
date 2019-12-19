title: SwishNet Korean
author: Naomi Ceder
source: https://pycon.blogspot.com/2016/03/why-not-join-sprints-this-year-at-pycon.html 

#  SwishNet

Posted by [ Naomi Ceder ](https://www.blogger.com/profile/09984149935314850807 "author profile")
[ March 10, 2019  ](https://pycon.blogspot.com/2016/03/why-not-join-sprints-this-year-at-pycon.html "permanent link")

## C.SwishNet A 1D Convolutional Neural Network

SwishNet is a 1D convolutional network which operates on frame-wise MFCC features. The rationale behind designing a 1D CNN instead of a conventional 2D CNN (that operates on spectrogram) is that, 1D convolutions are much less computationally expensive and 1D feature maps require less memory during processing. Also, a carefully designed 1D CNN is likely to be much smaller in size than a 2D CNN. In SwishNet, convolutions are carried out along the temporal dimension only. The MFC coefficients are treated as input channels. This approach is based on the fact that MFC coefficients are are almost uncorrelated, like the channels (i.e. Red, Green, Blue) of an image.

Our network architecture is partly inspired by the new WaveNet [7] architecture and the Inception [9] architecture. Like the WaveNet architecture, we used multiple layers of causal convolutions to gradually increase the receptive field and gated activation functions [10] containing sigmoid and tanh functions instead of widely used ReLU activations. The gated activations allow the network to select which information to pass from one time step to the next, just like a gated recurrent network. It also conveniently cuts down the number of feature maps passing from one layer to the next to half.

The WaveNet architecture was originally designed for autoregressive audio generation, but our network is focused on classification. So, we used strided convolutions instead of dilated convolutions to reduce the computational cost. Adding residual and skip connections improved both accuracy and ease of training. Residual connections were applied to the middle layers and skip connections were applied to the last three layers.

The WaveNet architecture was designed for audio signals with high sampling rates, so an exponential increase of the receptive field was encouraged. But, our network operates on derived features, so we used longer filters with overlapping receptive fields and allowed a more gentle increase of the receptive field from lower to upper layers in order to prioritize the capture of more recent information in the lower layers.

We found that it is vital to capture long-term dependencies i.e. information in the previous frames in the lower layers, which requires longer filters. The inception architecture uses parallel branches to capture features at different scales. With a similar purpose in mind, we added depthwise separable 1D convolutional [11] layers with longer (kernel size 6) un-dilated filters parallel to the conventional convolutional filters and concatenated the outputs from the two branches. Separable convolutions are faster and require less memory than conventional convolutions, especially for longer filter kernels, so this modification further improved accuracy without compromising size and speed.

As a regularization method, we (optionally) added dropout layers (not shown in the figure) in between convolutional blocks which slightly improved accuracy. However, depending on the size of the dataset and the width of the network, it may not be necessary.


