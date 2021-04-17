# Expression Transfer Using Flow-based Generative Models

This repository contains the results obtained from the experiments on expression transferring performed with the [Glow model](https://github.com/openai/glow) from *OpenAI*.

In this work, we continued with the approach of attribute manipulation proposed in the original [Glow paper](https://arxiv.org/abs/1807.03039), but instead of targeting attribute manipulation, we explored if the same technique could be used to transfer facial expressions between a source identity and a target identity. This approach could be the basis for creating deepfake content via face-swapping with flow-based generative models. Flow-based methods eliminate the necessity of person-specific training due to the property of invertibility, being able to reconstruct any input image almost perfectly to human perception. Therefore, flow-based models are special candidates for deepfake methods since they could be able to create deepfakes of whoever the identity without training on this concrete person. Finally, in addition to the method for the transfer, we propose an evaluation pipeline of the generated images in terms of how similar the identity of the obtained image is with respect to its own identity (and with respect to the source identity) and quality of the facial expression itself in terms of Action Units.

The results can be easily reproduced by using our [Colab notebook](https://colab.research.google.com/drive/11npIn2RwZrQf7LIoOg548VvoDafzp2cz?authuser=1).

## Proposed approach
In this work, we proposed the usage of the vector arithmetics traditionally used for attribute manipulation, but for expression transferring between identities. The proposed equation could be mathematically defined as follows:

![alt text](https://github.com/aandvalenzuela/normalizing-flows/blob/main/results/transformation.png)

Note that the expression vector is computed as the difference between the image with the original expression to be transferred (*source input*) and the mean face of the source identity (*source mean*). The mean face of an identity is defined as a its neutral and frontal (expressionless) face computed as the mean of the vectors encoding the original images in the latent space. As for attribute manipulation, the coefficient *α* is the one regulating the intensity of such transfer.

## Results
The following image illustrates the proposed approach for ```α=0.5```:

![alt text](https://github.com/aandvalenzuela/normalizing-flows/blob/main/results/schema.png)
The general trend shows that the *α* range in which the expression transfer is present corresponds to *[0.3,0.5]*. The transfer for this concrete range could be observed in the following images (from *0.3* to *0.5*, in steps of *0.02*):

![alt text](https://github.com/aandvalenzuela/normalizing-flows/blob/main/results/transferring/expression5/emma.png)

## Evaluation pipeline
We also proposed an evaluation pipeline for the generated images in terms of the likeliness of the generated image with respect to the mean of the same identity and of the source identity, and the quality of the expression transfer itself in terms of Action Units (AUs).

In order to analyse the likeliness between the target output image and both target and source means, the CNN *Inception Resnet (V1)* is used following a [pytorch implementation](https://github.com/timesler/facenet-pytorch) pretrained on both *VGGFace2* and *CASIA-Webface* datasets. This implementation of the *Inception Resnet* returns a distance score for each pair of input images.

In order to evaluate the expressions obtained in terms of their quality, the toolkit [OpenFace 2.0](https://github.com/TadasBaltrusaitis/OpenFace) is used.
This toolkit recognizes facial expressions through detecting both intensity and presence of 18 AUs. The intensity is on a 5 point scale (0: not present to 5: maximum intensity) representing the degree of activation.


## Repository structure
The ```data``` folder contains the original data used for the experiments. In concrete, it contains the folder ```expressions``` in which the expressions to be transferred are present, and other folders (```id$_aligned```) with aligned facial images of five different identities directly crawled from the Internet.

The ```results``` directory contains a ```means``` folder with the expressionless images obtained as the average of the original images present in ```data/id$_aligned```. It also contains a ```transferring``` directory with the results of the transfer for some of the expressions present in ```data/expressions``` between the considered identities.

The ```evaluation``` folder contains the ```likeliness``` directory with the *.csv* files containing the distance score for the generated images with respect to their corresponding source and target means. It also contains a ```quality``` directory with the *.csv* files containing the analysis of the expressions in terms of intensity of AUs. This last folder also contains the ```raw_data``` obtained after processing the generated images with the *OpenFace 2.0* toolkit.
