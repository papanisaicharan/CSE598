
# GAN-based Defense Against Adversarial Attacks: Enhancements and Exploration

Main aim of this project is to implement the paper [Cowboy defence](https://arxiv.org/pdf/1805.10652.pdf "Cowboy defence") and try to improve or do experimentation for better results.

We have successfully implemented the existing paper but couldn't do all kind of experimentation due to lack of time and high computing power.

## Experimentation

We used the MNIST dataset, consisting of 60,000 grayscale images of handwritten digits ranging from 0 to 9, for our experimentation. While the existing paper used multiple datasets, we opted to use only the MNIST dataset due to time constraints for the experiment and based on the professor's instructions.

We trained and validated a CNN classifier on the MNIST dataset with a 9:1 ratio of train and validation sets. We used the SGD optimizer and cross-entropy loss function, along with the following parameters:
- learning rate: 0.001
- momentum: 0.9
- total epochs : 100
- batch size : 50
- valid size : 10000

[![Accuracy Graph](https://github.com/papanisaicharan/CSE598/blob/3a30d43eae180287b69ab9a27414ca46de467d22/readme-Image/Accuracy%20graph.png "Accuracy Graph")](https://github.com/papanisaicharan/CSE598/blob/3a30d43eae180287b69ab9a27414ca46de467d22/readme-Image/Accuracy%20graph.png "Accuracy Graph")

[![Loss graph](https://github.com/papanisaicharan/CSE598/blob/main/readme-Image/Loss%20graph.png "Loss graph")](https://github.com/papanisaicharan/CSE598/blob/main/readme-Image/Loss%20graph.png "Loss graph")


Transformations, such as horizontal flipping and normalization, are applied to the inputs before they are passed to the model for training. In the end, we were able to achieve a training accuracy of 98% with a training loss of 0.02. Please refer to above figures for an overview of how the training and validation losses and accuracy changed over the epochs.

Later, we tested our model on the test set, which consists of 10,000 images, and achieved an accuracy of 98.0800%.

Next, we needed adversarial samples to use when performing defence using the cowboy defence. So, we used the FSGM attack (Foolbox library) and created adversarial samples using the test set. These adversarial samples were stored in pickle files and were later used to directly load the images.


We then built the GAN, specifically DCGAN, which consists of the generator and discriminator. It was trained using the original dataset by applying similar transformations described above. We used the Adam optimizer, binary cross-entropy loss function, and the following parameters:
- learning rate: 0.0002
- Decay of first order momentum of the gradient (b1) : 0.5
- Decay of second order momentum of the gradient (b2) : 0.999
- Dimensionality of the latent space : 100
- total epochs : 500
- batch size : 50

We used the PyTorch Lightning library to automate the training and collecting logs, etc. Refer to below image for GAN generated images, discriminator and generator losses over the period of total epochs.

[![GAN generated images and Losses](https://github.com/papanisaicharan/CSE598/blob/main/readme-Image/GAN%20Graphs.png "GAN generated images and Losses")](https://github.com/papanisaicharan/CSE598/blob/main/readme-Image/GAN%20Graphs.png "GAN generated images and Losses")


After the GAN was trained and ready, we applied cowboy defence on both the clean dataset and adversarial sample dataset generated using FSGM. In simple terms, we used cowboy to clean or correct the adversarial samples before passing them to the classifier. We tried to find a sweet spot that could balance accuracy and robustness by varying sigma values. We experimented with 0.01, 0.1, 1, 10, and 100 sigma values which ran over 200 cleaning steps in the cowboy architecture. We also focused on optimizing the GAN architecture (which can be found on GitHub) and improving the effectiveness of the defence. Please refer I for the accuracy reported by the classifier when samples are cleaned over 200 cleaning steps with cowboy with respect to sigma values.

| Sigma | Test Accuracy on Adversarial Samples  | Test Accuracy on Clean dataset  |
| :------------: | :------------: | :------------: |
|  0.01 | 1483 | 0.1186
|  0.1 |  0.1197 | 0.1111
| 1 |  0.1104 | 0.1108
|  10 |   0.1148 | 0.1118
| 100  | 0.1078 | 0.1073


## Conclusions

In the original paper, the classifier was tested with 5000 cleaning steps in the cowboy architecture. As a result, they achieved good accuracy (around 78%) with a clean dataset, but low accuracy (12%) with adversarial samples generated using FSGM. However, in our case, we obtained around 11% accuracy on a clean dataset and 14% accuracy on adversarial samples with a sigma of 0.01. Therefore, we can claim the
following results:
- Increasing the number of cleaning steps in cowboy
(GAN) directly increases the test accuracy of the classifier
on a clean dataset. The number of cleaning steps is
directly proportional to the test accuracy on a clean
dataset.
- Increasing the number of cleaning steps in cowboy in-
versely decreases the test accuracy of the classifier on
adversarial samples. The number of cleaning steps is in-
versely proportional to the test accuracy on an adversarial
dataset.
- The number of cleaning steps can be one of the deciding
factors in increasing the accuracy on both adversarial and
clean datasets.

Interestingly, we achieved around 98% accuracy on the test set for the simple CNN, which is better than what was mentioned in the original paper. While applying the defense using GAN on adversarial samples and clean datasets, we collected a few images with differences. Please refer below image for clear understanding.

[![Images extracted with cowboy defense](https://github.com/papanisaicharan/CSE598/blob/main/readme-Image/Defence%20Images.png "Images extracted with cowboy defense")](https://github.com/papanisaicharan/CSE598/blob/main/readme-Image/Defence%20Images.png "Images extracted with cowboy defense")

Note: We could not run all the experimentations that we initially thought of due to lack of time and GPU access constraints.


## Future work

- Create and train multiple GANs and choose the best ones
by using the confidence scores reported by these GANs,
similar to a random forest-based approach. This could
significantly improve defense against attacks.
- Rather than relying solely on the original paper for
comparisons, we can increase the number of cleaning
steps and conduct additional experimentation to gain
further insights.
- In addition, we can attempt to create adversarial samples
using other datasets and various other attack methods
