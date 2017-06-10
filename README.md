# HMI enhancer

`Enhance` is a deep learning technique for deconvolving and superresolving HMI continuum images and magnetograms. It is described in the paper, that you can find in [https://arxiv.org/abs/X](https://arxiv.org/abs/enlace).

![example](docs/imagen.gif?raw=true "")
**Figure 1** — Example of `Enhance` applied to real images.

## Abstract

The Helioseismic and Magnetic Imager (HMI) provides continuum images and magnetograms with a cadence better than one every minute. It has been continuously observing the Sun 24 hours a day for the past 7 years. The obvious trade-off between cadence and spatial resolution makes that HMI is not enough to analyze the smallest-scale events in the solar atmosphere.
 
Our aim is developing a new method to enhance HMI data, simultaneously deconvolving and superresolving images and magnetograms. The resulting images will mimick observations with a diffraction-limited telescope twice the diameter of HMI. The method, that we term `Enhance`, is based on two deep fully convolutional neural networks that input patches of HMI observations and output deconvolved and superresolved data. The neural networks are trained on synthetic data obtained from simulations of the emergence of solar active regions.
 
We have obtained deconvolved and supperresolved HMI images. To solve this ill-defined problem with infinite solutions we have used a neural network approach to add prior information from the simulations. We test `Enhance` against Hinode data that has been degraded to a 28 cm diameter telescope showing very good consistency. The code is open sourced for the community.


## Using `Enhance` for prediction
A list of example command lines you can use with the pre-trained models provided in the GitHub releases. The script we provide reads the observations from a FITS file, but you can modify the script to provide the observations from any other file. To change between each network you only have to change the parameter `-t` as follow:

```
python enhance.py -i samples/hmi.fits -t intensity -o output/hmi_enhanced.fits
```

```
python enhance.py -i samples/blos.fits -t blos -o output/blos_enhanced.fits
```

We provide two files to test `Enhance` (Fig. 1). The intensity images must be normalized to the quiet sun intensity and the magnetogram must be in kG.

## Using `Enhance` for training

Pre-trained models are provided in the GitHub releases. If you want to train `Enhance` with your own images, we provide two scripts `train.py` (intensity network) and `train_blos.py` (magnetogram network) to this aim.

```
python train.py --output=networks/test --epochs=20 --depth=5 --kernels=64 --action=start --model=keepsize --activation=relu --lr=1e-4 --lr_multiplier=1.0 --batchsize=32 --l2_regularization=1e-8
```

The parameters are described here:


    --action={start,continue}
        `start`: start a new calculation
        `continue`: continue a previous calculation
    --epochs=20
        Number of epochs to use during training
    --output=networks/keepsize_relu 
        Define the output file that will contain the network topology and weights
    --depth=5
        Number of residual blocks used in the network. This number will affect differently depending on the topology of the network
    --model={keepsize,encdec}
        `keepsize` is a network that maintains the size of the input and output, with an eventual upsampling at the end in case of superresolution
        `encdec` is an encoder-decoder network
    --padding={zero,reflect}
        `zero` uses zero padding for keeping the size of the images through the network. This might produce some border artifacts 
        `reflect` uses reflection padding, which strongly reduces these artifacts
    --activation={relu,elu}
        Type of activation function to be used in the network, except for the last convolutional layer, which uses a linear activation

## Troubleshooting Problems

1.- You must update tensorflow to the last version. Message:
```
AttributeError: 'module' object has no attribute 'convolution'
```

2.- You must update keras to the version 2. Message:
```
ImportError: cannot import name conv_utils
```
