# Introduction


```python
from flowvae.base_model import convEncoder_Unet, convDecoder_Unet

_encoder = convEncoder_Unet(in_channels=2, last_size=[5], hidden_dims=[64, 128, 256])
_decoder = convDecoder_Unet(out_channels=1, last_size=[5], hidden_dims=[256, 128, 64, 64], sizes = (24, 100, 401), encoder_hidden_dims=[256, 128, 64, 2])
```
The table below shows the available encoder and decoder.

| Type | Encoder | Decoder | 
|--|--|--|
| base class| `Encoder` | `Decoder` | 
| dense connected | `mlpEncoder` | `mlpDecoder` | 
| convolution (1D/2D) | `convEncoder` | `convDecoder` | 
| Unet convolution (1D/2D) | `convEncoder_Unet` | `convDecoder_Unet` | 
| ResNet | `Resnet18Encoder` | `Resnet18Decoder` |  


> **Remark:** The flowfield has spacial correlations. The densely connected network ignores such correlations because it takes the adjusted points in flowfields/profiles separately. Therefore, it is recommended to use convolutional networks to capture the correlations and reduce the size of trainable parameters.


![](_static/images/models/1dconvencoder.png "Sketch of the 1d convolution network encoder")


- `in_channels`: (`int`)
    
    The size of channels of the profiles input to the encoder. Each channel can represent a flow variable (i.e., x, y, p, T, u, v)

- `last_size`: (`List` of `int`)
    
    The size of the feature map that is closest to the latent variables (for the encoder is the last feature map). Notice that the list does not contain the channel size.
