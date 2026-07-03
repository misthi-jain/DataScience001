# MNIST Denoising Autoencoder

Week 6 Assignment - Built a deep learning model that removes noise from images using an autoencoder trained on the MNIST dataset.

## What Is This Project About

An autoencoder is a neural network that learns to compress an image into a smaller representation (encoder) and then reconstruct it back (decoder). For denoising, instead of just reconstructing the same image, the model is trained to take a **noisy image as input** and output the **clean version**. This forces it to learn what the actual digit looks like, ignoring the noise.

## Dataset

Used the MNIST dataset (handwritten digits 0-9) from Kaggle: https://www.kaggle.com/datasets/awsaf49/mnist-dataset
- 60,000 training images
- 10,000 test images
- Each image is 28x28 grayscale

## Approach

1. Loaded the MNIST png dataset and normalized pixel values to [0,1]
2. Added Gaussian noise to create noisy versions of the images (noise_factor = 0.5)
3. Built a convolutional autoencoder:
   - Encoder: Conv2D + MaxPooling layers to compress the image
   - Decoder: Conv2D + UpSampling layers to reconstruct it back to 28x28
4. Trained the model using noisy images as input and clean images as target, with binary crossentropy loss and Adam optimizer
5. Generated denoised outputs on the test set and compared original vs noisy vs denoised

## Model Architecture

| Layer | Output Shape |
|---|---|
| Input | (28, 28, 1) |
| Conv2D + MaxPooling | (14, 14, 32) |
| Conv2D + MaxPooling | (7, 7, 32) |
| Conv2D + UpSampling | (14, 14, 32) |
| Conv2D + UpSampling | (28, 28, 32) |
| Conv2D (output) | (28, 28, 1) |

Total params: 28,353

## Results

Trained for 20 epochs, final loss: 0.0955, val_loss: 0.0954

**Evaluation Metrics (on test set):**
- Average PSNR: 20.08 dB
- Average SSIM: 0.871

![results](results/output_comparison.png)

The model successfully removes noise while preserving the digit's shape and structure.

## Observations

- The autoencoder successfully removes Gaussian noise while preserving the digit's core structure
- Denoised outputs are slightly blurred compared to originals, which is expected since the model compresses the image into a small latent space and loses some fine detail
- Training was stable — loss and val_loss stayed close throughout, so no overfitting
- Trained on CPU (~40s/epoch), would be faster with GPU access
- Possible improvements: deeper encoder/decoder, tuning noise_factor, or trying a variational autoencoder for smoother reconstructions

## Tech Used

- TensorFlow / Keras
- NumPy
- OpenCV (for loading images)
- Matplotlib (for visualization)
