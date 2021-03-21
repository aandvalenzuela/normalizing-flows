# Expression Transfer Using Flow-based Generative Models

This repository contains the results obtained from the experiments on expression transferring performed with the [Glow model](https://github.com/openai/glow) from OpenAI.

In this work, we continued with the approach of attribute manipulation proposed in the original [Glow paper](https://arxiv.org/abs/1807.03039), but instead of targeting attribute manipulation, we explored if the same technique could be used to transfer expressions between a source identity and a target identity. This approach could be the basis for creating deepfake content via face-swapping with flow-based generative models. Flow-based methods eliminate the necessity of person-specific training due to the property of invertibility being able to reconstruct any input image almost perfectly to human perception. Therefore, flow-based models are special candidates for deepfake methods since they could be able to create deepfakes of whoever the identity without training on this concrete person. Finally, in addition to the method itself for the transfer, we propose an evaluation pipeline of the generated images in terms of how similar the identity of the obtained image is with respect to its own identity (and with respect to the source identity) and quality of the facial expression itself in terms of Action Units.

The results can be easily reproduced by using our [Colab notebook](https://colab.research.google.com/drive/11npIn2RwZrQf7LIoOg548VvoDafzp2cz?authuser=1).

## Proposed approach
In this work, we proposed the usage of the vector arithmetics used for attribute manipulation, but for expression transferring between identities. The proposed equation could be mathematically defined as follows:

```
\begin{equation}\label{proposed_eq}
\mathbf{x}_{\text{target output}} = \mathbf{z}_{\text{target mean}} + \alpha \cdot \mathbf{z}_{\text{expression vector}},
\end{equation}
```
where ```$\mathbf{z}_{\text{expression vector}} = \mathbf{z}_{\text{source input}} - \mathbf{z}_{\text{source mean}}$``` is the expression vector computed as the difference between the image with the original expression that wants to be transferred (source input) and the mean face of the source identity (source mean). The mean face of an identity is defined as a its neutral and frontal (expressionless) face computed as the mean of the vectors encoding the original images in the latent space. As for attribute manipulation, the coefficient $\alpha$ is the one regulating the intensity of such transfer.

## Evaluation pipeline
We also proposed an evaluation pipeline for the generated images in terms of the likeliness of the generated image with respect to the mean of the same identity and to the source identity, and the quality of the expression transfer itself in terms of Action Units (AUs).

In order to analyse the likeliness between the target output image and both target and source means, the CNN Inception Resnet (V1) is used following an in [pytorch](https://github.com/timesler/facenet-pytorch) pretrained on both VGGFace2 and CASIA-Webface datasets webface. This implementation of the Inception Resnet returns a distance score for each pair of input images.

In order to evaluate the expressions obtained in terms of their quality, the toolkit [OpenFace 2.0](https://github.com/TadasBaltrusaitis/OpenFace) is used.
This toolkit recognizes facial expressions through detecting both intensity and presence of 18 AUs. The intensity is on a 5 point scale (0: not present to 5: maximum intensity) representing the degree of activation.


## Repository structure
