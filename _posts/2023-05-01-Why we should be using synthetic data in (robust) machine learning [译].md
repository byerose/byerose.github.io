---
layout: post
title: Why we should be using synthetic data in (robust) machine learning [译]
date: 2023-05-01 20:40:16
description: 
tags: 论文 精读 鲁棒 生成
categories: 精读论文
---

---
title: Why we should be using synthetic data in (robust) machine learning [译]
feed: show
date: 01-05-2023
---

# Why we should be using synthetic data in (robust) machine learning?[译]

[Why use synthetic data in machine learning?](https://vsehwag.github.io/blog/2022/4/synthetic_data_in_ml.html)

## **Why we should be using synthetic data in (robust) machine learning**

**为什么我们应该在（鲁棒的）机器学习中使用合成数据**

25 April 2022 

If you are excited about generative models, then you would find the latest improvements (e.g., [DALLE-2](https://openai.com/dall-e-2/) from OPENAI) nothing less than magical. These models excel at generating novel yet realistic synthetic images [[1](https://arxiv.org/abs/2112.10741), [2](https://arxiv.org/abs/2105.05233), [3](https://arxiv.org/abs/2202.00273)]. Evident to this success are thousands of dalle-2 images floating on [twitter](https://twitter.com/hashtag/dalle), all of which are photorealistic synthetic images generated by this new model. Even before dalle-2, we have previous works that successfully solve this task on small scale but challenging datasets like ImageNet [[1](https://arxiv.org/abs/2105.05233), [2](https://cascaded-diffusion.github.io/)]. A large part of recent progress is fueled by work on **diffusion models**, a highly successful class of generative models [[1](https://arxiv.org/pdf/2112.10752.pdf), [2](https://arxiv.org/abs/2112.07804), [3](https://arxiv.org/abs/2112.05744), [4](https://arxiv.org/abs/2106.05931)].

---

如果您对生成模型感兴趣，那么你会发现最新的研究成果非常神奇（例如来自 OPENAI 的 [DALLE-2](https://openai.com/dall-e-2/)）。这些模型擅长生成新颖且逼真的合成图像 [[1](https://arxiv.org/abs/2112.10741), [2](https://arxiv.org/abs/2105.05233), [3](https://arxiv.org/abs/2202.00273)]。这一成功的证据是 [twitter](https://twitter.com/hashtag/dalle) 上流传着数千张 dalle-2 图像，所有这些图像都是由这个新模型生成的逼真的合成图像。甚至在 dalle-2 之前，我们之前的工作已经成功地在小规模但具有挑战性的数据集上解决了这个任务，如 ImageNet [[1](https://arxiv.org/abs/2105.05233), [2](https://cascaded-diffusion.github.io/)]。最近取得的进展很大一部分是由**扩散模型**的工作推动的，扩散模型是一类非常成功的生成模型 [[1](https://arxiv.org/pdf/2112.10752.pdf), [2](https://arxiv.org/abs/2112.07804), [3](https://arxiv.org/abs/2112.05744), [4](https://arxiv.org/abs/2106.05931)]。

![(a) Stochastic-Backprop ([Rezende et al., 2014](https://arxiv.org/pdf/1401.4082.pdf))](https://vsehwag.github.io/blog/2022/4/data/rezende_synthetic.png)

(a) Stochastic-Backprop ([Rezende et al., 2014](https://arxiv.org/pdf/1401.4082.pdf))

![(b) GAN ([Goodfellow et al., 2014](https://arxiv.org/abs/1406.2661))](https://vsehwag.github.io/blog/2022/4/data/goodfellow_synthetic.png)

(b) GAN ([Goodfellow et al., 2014](https://arxiv.org/abs/1406.2661))

![(c) Diffusion model ([Ho et al., 2020](https://arxiv.org/pdf/2006.11239.pdf))](https://vsehwag.github.io/blog/2022/4/data/ddpm_synthetic.png)

(c) Diffusion model ([Ho et al., 2020](https://arxiv.org/pdf/2006.11239.pdf))

![(d) DALL·E 2 ([Ramesh et al., 2022](https://cdn.openai.com/papers/dall-e-2.pdf))](https://vsehwag.github.io/blog/2022/4/data/dalle2_synthetic.png)

(d) DALL·E 2 ([Ramesh et al., 2022](https://cdn.openai.com/papers/dall-e-2.pdf))

- Figure-1. **Progress in generative models.** 图1 - 生成模型的进展
    
    Each subfigure presents synthetic/fake images from different generative models across years. While generative models have consistently made progress over years, recent class of diffusion based generative models finally brings the transformative capabilities of generative models to large, diverse, and challenging datasets (fig. c, d). For example, the dalle-2 (fig. d) generative model, which uses a diffusion model, can generate highly realistic images across a wide range of novel scenarios. In this post, I'll mainly discuss how we can improve representation learning using generative models, i.e., by distilling knowledge embedded inside the generative model.
    
    ---
    
    每个子图都展示近些年不同生成模型合成/假图像。虽然生成模型多年来一直在取得进展，但最近一类基于扩散的生成模型最终将生成模型的革命性能力带到了大型、多样化和具有挑战性的数据集上（图c、d）。例如，使用扩散模型的 dalle-2（图 d）生成模型可以在各种新颖场景中生成高度逼真的图像。在这篇文章中，我将主要讨论**如何使用生成模型改进表示学习，即通过提取嵌入在生成模型中的知识**。
    

When looking over the super realistic synthetic/fake images from modern generative model, a natural question to ask here is whether we can utilize these images to improve representation learning itself. Broadly speaking, can we transfer the knowledge embedded into generative models to downstream discriminative models. The most common usage of a similar phenomenon is in robotics and reinforcement learning, where an agent first learns to solve the task in a simulation and then transfers the learned knowledge to the real environment [[1](https://lilianweng.github.io/posts/2019-05-05-domain-randomization/), [2](https://ai.bu.edu/syn2real/)]. Here the simulator, built by us, acts as an approximate model of real world conditions. However, access to such a simulator is only accessible in certain scenarios (e.g., object manipulation by robots, autonomous driving). In contrast, a generative model learns an approximative model of real world using the training data. Thus for the task of having an approximation of real world model, it shifts the objective from **manual designing to learning it from data**, an approach that can democratize the **synthetic-to-real** learning approach. In other words, generative models can acts as **universal simulators**, due to applicability of their learning based approach to numerous applications. If we can transfer knowledge embedded in these generative models, then this approach has the potential to transform machine learning at broad scale.

---

在查看来自现代生成模型的超逼真合成/假图像时，一个很自然的问题是我们是否可以利用这些图像来改进表示学习本身。从广义上讲，我们能否将生成模型中嵌入的知识转移到下游的判别模型中。类似现象最常见的用法是在机器人技术和强化学习中，代理首先学习在模拟中解决任务，然后将学到的知识转移到真实环境中[[1](https://lilianweng.github.io/posts/2019-05-05-domain-randomization/), [2](https://ai.bu.edu/syn2real/)]。其中，我们设计的模拟器充当真实环境的近似模型。然而，只有在某些情况下才能访问这种模拟器（如机器人操纵物体、自动驾驶）。相反，生成模型使用训练数据学习真实环境的近似模型。因此，为了完成近似真实世界的模型的任务，目标将**从手动设计变为到从数据中学习**，这种方法可以使**从生成到真实**的学习方法大众化。换句话说，生成模型可以充当**通用模拟器**，因为此类基于学习的方法适用于众多应用。如果我们能够迁移这些生成模型中嵌入的知识，那么这种方法就有可能大规模地改变机器学习。

![(a) Conventional learning from data](https://vsehwag.github.io/blog/2022/4/data/conventional_learning.png)

(a) Conventional learning from data

![(b) Simulator assisted learning](https://vsehwag.github.io/blog/2022/4/data/simulator_learning.png)

(b) Simulator assisted learning

![(c) Generative model assisted learning](https://vsehwag.github.io/blog/2022/4/data/generative_learning.png)

(c) Generative model assisted learning

- Figure-2. **Democratizing simulation-to-real learning with generative models.** 图 2 - 使用生成模型将模拟到真实的学习民主化
    
    When it comes to learning with generative models, one can draw similarities with well established *sim-to-real* approach [[1](https://lilianweng.github.io/posts/2019-05-05-domain-randomization/), [2](https://ai.bu.edu/syn2real/)]. In *sim-to-real* approach, we first learn representations in a simulated environment and utilize the acquired knowledge in real world tasks. We design such simulators to best immitate real world environment (e.g., in autonomous driving), but such simulators are only feasible for a handful of domains. In contrast, generative models can learn an approximation of real world environment from raw data. So the democratization comes from shifting the problem from *designing* an approximation of the real world environment to *learning* it from data, something deep learning is highly effictive at. The concrete research problem is how to best distill the knowledge from generative models for the downstream representation learning. As we will discuss next, the most straightforward way to incorporate generative models is by training on large amounts of synthetic images from it. Surprisingly this simple approach is highly effective in improving performance in common machine learning tasks.
    
    ---
    
    在使用生成模型进行学习时，可以得出与完善的模拟到真实方法 [1、2] 的相似之处。在 sim-to-real 方法中，我们首先在模拟环境中学习表示，并在现实世界的任务中利用获得的知识。我们设计此类模拟器以最好地模仿现实世界环境（例如，在自动驾驶中），但此类模拟器仅适用于少数几个领域。相反，生成模型可以从原始数据中学习对真实世界环境的近似。因此，民主化来自于将问题从设计真实世界环境的近似值转移到从数据中学习，而深度学习在这方面非常有效。具体的研究问题是如何最好地从生成模型中提取知识，用于下游表示学习。正如我们接下来将要讨论的那样，合并生成模型的最直接方法是对来自它的大量合成图像进行训练。令人惊讶的是，这种简单的方法在提高常见机器学习任务的性能方面非常有效。
    

**Knowledge distillation from generative models.** The success of generative models assisted learning critically depends on how well we can distill the knowledge from it. We may even need customized methods for different generative models. For example, while GANs can be modified to learn unsupervised representations simultaneously with generative models ([BigBiGAN](https://www.deepmind.com/open-source/bigbigan)), such effective techniques haven't yet been developed for diffusion models. In contrast, a more intuitive and straightforward approach is applicable to all generative models: Extracting synthetic data from generative models and use it for training downstream models. We will consider this approach in most of our experiments in the post. While there is significant room to improve beyond it, it should serve as a common baseline for follow-up methods. I'll further discuss this direction at the end of the post.

---

**从生成模型中提取知识**。生成模型辅助学习的成功关键取决于我们如何从中提取知识。我们甚至可能需要为不同的生成模型定制方法。例如，虽然可以修改 GAN 以与生成模型 ([BigBiGAN](https://www.deepmind.com/open-source/bigbigan)) 同时学习无监督表示，但尚未开发出适用于扩散模型的有效技术。相比之下，一种更直观、更直接的方法适用于所有生成模型：从生成模型中提取合成数据，并将其用于训练下游模型。我们将在本文的大部分实验中考虑这种方法。尽管除此之外还有很大的改进空间，但它应该作为后续方法的共同基线。我将在文章末尾进一步讨论这个方向。

![https://vsehwag.github.io/blog/2022/4/data/overview.jpeg](https://vsehwag.github.io/blog/2022/4/data/overview.jpeg)

- Figure-3. **Learning with synthetic data.** 图 3 - 学习合成数据
    
    Overview of the pipeline where we distill knowledge from generative models by extracting a large amount of synthetic data from them and then using it in downstream representation learning.
    
    我们通过从生成模型中提取大量合成数据来从生成模型中提取知识，然后将其用于下游表示学习的流程概述。
    

Figure 3 summarizes our approach. We start with the images in the training dataset and train a generative model using them. An example would be training a DDPM ([Ho et al.](https://arxiv.org/abs/2006.11239)) or StyleGAN ([Karras et al.](https://arxiv.org/abs/2006.06676)) model using the 50,000 images in the cifar10 dataset. Once trained, the generative model gives us the ability to sample a very large number of synthetic images from it. In the following experiments, we will commonly sample one to ten million images. Finally, we combine the synthetic data with the real training images and train the classifier on the combined dataset. The hypothesis here is that the additional synthetic data will boost the performance of the classifier.

---

图 3 总结了我们的方法。我们从训练数据集中的图像开始，并使用它们训练生成模型。例如，使用 cifar10 数据集中的 50,000 张图像训练 DDPM ([Ho et al.](https://arxiv.org/abs/2006.11239)) 或 StyleGAN ([Karras et al.](https://arxiv.org/abs/2006.06676))  模型。一旦经过训练，生成模型使我们能够从中抽取大量合成图像。在接下来的实验中，我们通常会采样1~10m张图像。最后，我们将合成数据与真实训练图像结合起来，并在组合数据集上训练分类器。这里的假设是额外的合成数据将提高分类器的性能。

## **Part-1**

### **Inflection point with progress in generative models**

**第 1 部分：生成模型进展的拐点**

Using synthetic data from generative models in training is pretty straightforward.

*But would it help?*

---

在训练中使用来自生成模型的合成数据非常简单。

但这会有帮助吗？

![https://vsehwag.github.io/blog/2022/4/data/inflection_point.png](https://vsehwag.github.io/blog/2022/4/data/inflection_point.png)

- Figure-4. **Inflection point with progress in generative models.** 图 4 - 生成模型取得进展的拐点
    
     Our objective is to measure how much additional boost we get in performance when we train a classifier on the combined set of real and synthetic data. At start of progress in generative models, low-quality synthetic data would likely degrade performance. An inflection point occurs when the synthetic data provides an additional boost in performance. On different datasets, we observe a varying degree of progress beyond the inflection point. E.g., we have progressed much farther on cifar10 than the ImageNet dataset.
    
    ---
    
    我们的目标是衡量当我们在真实数据和合成数据的组合集上训练分类器时，我们在性能上获得了多少额外提升。在生成模型开始取得进展时，低质量的合成数据可能会降低性能。当合成数据提供额外的性能提升时，就会出现拐点。在不同的数据集上，我们观察到超过拐点的不同程度的进展。例如，与 ImageNet 数据集相比，我们在 cifar10 上取得了更大的进步。
    

**At start.** I would like to argue that it will likely demonstrate an *inflection point* with progress in the quality of synthetic data. In early stages of progress, the generative model will start to approximate real data distribution, but struggle to generate high fidelity images. E.g., synthetic images from some of the early work on GAN aren't highly photorealistic (fig. 1a, 1b). The synthetic data distribution learned by these early generative models will have a large gap from real data distribution, therefore simply combining these synthetic images with real data will lead to performance degradation. Note that this gap can be potentially minimized by using additional domain adaptation techniques [[1](https://arxiv.org/pdf/1409.7495.pdf)].

---

首先。我想争辩说，随着合成数据质量的进步，它可能会出现一个拐点。在进展的早期阶段，生成模型将开始逼近真实数据分布，但难以生成高保真图像。例如，来自 GAN 的一些早期工作的合成图像并不是非常逼真（图1a、1b）。这些早期生成模型学习到的合成数据分布与真实数据分布会有很大差距，因此简单地将这些合成图像与真实数据结合会导致性能下降。请注意，可以通过使用额外的域适应技术 [[1](https://arxiv.org/pdf/1409.7495.pdf)]来潜在地最小化这种差距。

**Near inflection point.** With improvement in generative models, one can start generating novel high fidelity images. Progress in generative models is a testament of it, where modern GANs generates stunning realistic image [[1](https://nvlabs.github.io/stylegan3/), [2](https://arxiv.org/pdf/2107.04589.pdf), [3](https://vsehwag.github.io/blog/2022/4/synthetic_data_in_ml.html)], requiring dedicated efforts to distinguish them from the real ones, i.e., deepfake detection [[1](https://arxiv.org/pdf/1812.08685.pdf), [2](https://arxiv.org/pdf/2006.07397.pdf)]. At this point, synthetic data certainly won't hurt the performance, since it lies very close to real-data distribution. But would it help, i.e., cross the inflection point?

---

在拐点附近。随着生成模型的改进，人们可以开始生成新颖的高保真图像。生成模型的进步证明了这一点，现代 GAN 生成令人惊叹的逼真图像 [[1](https://nvlabs.github.io/stylegan3/), [2](https://arxiv.org/pdf/2107.04589.pdf), [3](https://vsehwag.github.io/blog/2022/4/synthetic_data_in_ml.html)]，需要付出专门的努力才能将它们与真实图像区分开来，即 deepfake 检测[[1](https://arxiv.org/pdf/1812.08685.pdf), [2](https://arxiv.org/pdf/2006.07397.pdf)]。在这一点上，合成数据肯定不会影响性能，因为它非常接近真实数据分布。但这会有所帮助，即跨越拐点吗？

**Crossing inflection point.** To cross the inflection point, we not only need to generate novel high fidelity synthetic images but also achieve high diversity in these images. Synthetic images from GANs often lack diversity, which makes GANs not the most suitable choice. In contrast, diffusion based generative models achieve both high fidelity and diversity, simultaneously [[1](https://arxiv.org/abs/2105.05233)]. Across all datasets that we tested, we find that using synthetic images from diffusion models crosses the inflection point. Though how much it progresses beyond the inflection point varies across dataset. For example, while synthetic data from diffusion models bring a tremendous boost in performance on cifar10 dataset, it barely crosses the inflection point on ImageNet dataset.

---

跨越拐点。为了跨越拐点，我们不仅需要生成新颖的高保真合成图像，还要在这些图像中实现高度多样性。来自 GAN 的合成图像通常缺乏多样性，这使得 GAN 不是最合适的选择。相比之下，基于扩散的生成模型同时实现了高保真度和多样性 [[1](https://arxiv.org/abs/2105.05233)]。在我们测试的所有数据集中，我们发现使用来自扩散模型的合成图像跨越了拐点。尽管超过拐点的进展程度因数据集而异。例如，虽然来自扩散模型的合成数据在 cifar10 数据集上带来了巨大的性能提升，但它几乎没有超过 ImageNet 数据集上的拐点。

### **State-of-the-art: Have we crossed the inflection point on common vision datasets?**

**最先进的：我们是否跨越了常见视觉数据集的拐点？**

The short answer, **yes**. We consider four datasets, namely cifar10, cifar100, imagenet, and celebA. For each dataset, we aim to train two networks. One trained on only real images and the other trained on combination of both real and synthetic images. If latter network achieves better test accuracy, then we claim that the synthetic data crosses the inflection point, i.e., using synthetic data boost performance.

---

简短的回答，是的。我们考虑四个数据集，即 cifar10、cifar100、imagenet 和 celebA。对于每个数据集，我们的目标是训练两个网络。一个只训练真实图像，另一个训练真实图像和合成图像的组合。如果后一个网络达到更好的测试精度，那么我们声称合成数据越过拐点，即使用合成数据提升性能。

The next question is which experimental setup we should choose to study the impact of synthetic data. The first choice is baseline/benign training, i.e., training a neural network to achieve best generazation, i.e., test accuracy on images. However, we observe that synthetic data is even more helpful across a more challenging tasks, i.e., robust generalization (figure 5-a).

---

下一个问题是我们应该选择哪种实验装置来研究合成数据的影响。首选是基线/良性训练，即训练神经网络以实现最佳生成，即测试图像的准确性。然而，我们观察到合成数据在更具挑战性的任务中更有帮助，即鲁棒的泛化（图 5-a）。

### **Curious case of robust/adversarial training**

**鲁棒/对抗训练的奇特案例**

The objective in adversarial/robust training is to harden the classifiers against adversarial examples (provide link). Thus the metric of interest is the accuracy on test-set adversarial examples, i.e., robust accuracy. Surprisingly, defending against adversarial examples is extremely hard. State-of-the-art robust accuracy, even on simpler dataset like cifar10, remain quite low. It is well established the generalization with adversarial training requires significantly more data [[1](https://arxiv.org/abs/1804.11285)]. This high sample complexity of adversarial training likely leads to the higher benefit of synthetic data.

---

对抗性/鲁棒训练的目标是强化分类器以对抗对抗样本（提供链接）。因此，感兴趣的指标是测试集对抗样本的准确性，即鲁棒准确性。令人惊讶的是，防御对抗样本非常困难。即使在像 cifar10 这样更简单的数据集上，最先进的鲁棒精度仍然很低。众所周知，对抗训练的泛化需要更多的数据 [[1](https://arxiv.org/abs/1804.11285)]。对抗训练的这种高样本复杂性可能会导致合成数据的更高收益。

![(a) Success in benign and adversarial training](https://vsehwag.github.io/blog/2022/4/data/why_robust_training.png)

(a) Success in benign and adversarial training

![(b) Success with different datasets in training with adversarial training.](https://vsehwag.github.io/blog/2022/4/data/improvement_robust_accuracy.jpeg)

(b) Success with different datasets in training with adversarial training.

- Figure-5. 图 5
    
    (a) **Why study robust training.** We first show that the impact of synthetic data is much more significant in robust training then the regular/benign training. This is particularly due to higher sample complexity of robust training. 
    
    (b) We measure the benefit of training on both synthetic and real images, compared to just real images, on common image datasets.
    
    ---
    
    (a) 为什么要研究鲁棒训练。我们首先表明，合成数据的影响在鲁棒训练中比在常规/良性训练中更为重要。这尤其是由于鲁棒训练的样本复杂性较高。 
    
    (b) 我们衡量了在普通图像数据集上对合成图像和真实图像进行训练与仅对真实图像进行训练相比的好处。
    

Across all four datasets, we find that training with combined real and synthetic data achieved better performance than training only on real data (Figure 5-b). However, the impact of synthetic data varies with datasets, e.g., in comparison to cifar10, the benefit on ImageNet is quite small. It brings us to the discussion on the inflection point, where the success of generative models varies across datasets. ImageNet is strictly a harder dataset than cifar (more number of classes, diverse images), making it much harder for generative models to generate both high quality and diverse images on this dataset.

---

在所有四个数据集中，我们发现结合真实数据和合成数据进行训练比仅对真实数据进行训练取得了更好的性能（图 5-b）。然而，合成数据的影响因数据集而异，例如，与 cifar10 相比，ImageNet 的优势非常小。它把我们带到了关于拐点的讨论，生成模型的成功因数据集而异。严格来说，ImageNet 是一个比 cifar 更难的数据集（更多的类别，不同的图像），这使得生成模型更难在此数据集上生成高质量和多样化的图像。

## **Part-2**

### **Understanding why synthetic helps (its not just about photorealism)**

**第 2 部分：理解为什么合成有帮助（它不仅仅是[照片写实主义](https://baike.baidu.com/item/%E7%85%A7%E7%9B%B8%E5%86%99%E5%AE%9E%E4%B8%BB%E4%B9%89/624257)）**

The unique advantage of generative models is that we can sample unlimited amount of synthetic images from them. E.g., we used 1-10 million synthetic images for most experiments. But as we highlighted in figure 4, augmenting synthetic images help only when progress in generative have cross an inflection point. Before we quantify the progress in this section, here is a challenge.

---

生成模型的独特优势在于我们可以从中采样无限量的合成图像。例如，我们在大多数实验中使用了 1-10m张合成图像。但正如我们在图 4 中强调的那样，增强合成图像只有在生成的进展跨越拐点时才有用。在我们量化本节的进展之前，这里有一个挑战。

![(a) Real Images (CIFAR-10)](https://vsehwag.github.io/blog/2022/4/data/samples_images_real.png)

(a) Real Images (CIFAR-10)

![(b) [DDPM](https://arxiv.org/abs/2006.11239) ](https://vsehwag.github.io/blog/2022/4/data/samples_images_ddpm.png)

(b) [DDPM](https://arxiv.org/abs/2006.11239) 

![(c) [StyleGAN](https://arxiv.org/abs/2006.06676)](https://vsehwag.github.io/blog/2022/4/data/samples_images_styleC.png)

(c) [StyleGAN](https://arxiv.org/abs/2006.06676)

- Figure-6. **Which generative model is better?** 图 6 - 哪种生成模型更好？
    
    Can we identify which of the generative models (DDPM and StyleGAN) yields better quality synthetic images. We measure quality by the generalization accuracy on real images, i.e., when learning from synthetic data, how much accuracy we achieve on real data.
    
    我们能确定哪些生成模型（DDPM 和 StyleGAN）产生质量更好的合成图像吗？我们通过真实图像的泛化精度来衡量质量，即，当从合成数据中学习时，我们在真实数据上达到了多少精度。
    

In the figure above, we display real cifar-10 images for synthetic images from diffusion (DDPM) and stylegan based generative model. Our objective is to combine synthetic images from a generative models with real images in training cifar-10 classifier. Consider the following question: **Which of the two sets of synthetic images (DDPM vs StyleGAN) will be most helpful, when combined with real data?**

---

在上图中，我们显示了用于扩散 (DDPM) 和stylegan 的生成模型合成图像的真实 cifar-10 图像。我们的目标是在训练 cifar-10 分类器时将来自生成模型的合成图像与真实图像结合起来。考虑以下问题：当与真实数据结合时，两组合成图像（DDPM 与 StyleGAN）中的哪组最有帮助？

Both set of synthetic images are highly photo-realistic, but benefit of ddpm images significantly outperform styleGAN. For example, training with real+ddpm-synthetic images achieves more than 1-2% higher test accuracy than training on real+stylegan-synthetic images on cifar-10 dataset. The difference is even higher than 5-6% with robust training. The motivation behind this question was to highlight the challenge of identifying the best generative model, even for humans. This is because the quality of synthetic data for purpose of represnetation learning depends on both image quality and diversity. While humans are an excellent judge of former, we need a distribution level comparison to concretely measure both.

---

两组合成图像都非常逼真，但 ddpm 图像的优势明显优于 styleGAN。例如，在 cifar-10 数据集上，使用 real+ddpm-synthetic 图像进行训练比使用 real+stylegan-synthetic 图像进行训练的测试准确率高出 1-2% 以上。通过鲁棒的训练，差异甚至高于 5-6%。这个问题背后的动机是强调识别最佳生成模型的挑战，即使对于人类也是如此。这是因为用于表示学习的合成数据的质量取决于图像质量和多样性。虽然人类是前者的优秀判断者，但我们**需要一个分布水平比较来具体衡量两者**。

### **How real is fake data? Measuring distinguishability of real and synthetic data distributions.**

**假数据有多真实？测量真实和合成数据分布的可区分性。**

The common approach to measure the distribution distance between real and synthetic data using Fréchet inception distance ([FID](https://arxiv.org/abs/1706.08500)). FID simply measures the proximity of real and synthetic data using Wasserstein-2 distance in the feature space of deep neural network. So naturally the first approach would be to test if FID can explain why synthetic data from some generative models is more beneficial in learning than others. In particular, why diffusion models significantly more effective then contemporary GANs?

---

使用 Fréchet inception distance ([FID](https://arxiv.org/abs/1706.08500)) 测量真实数据和合成数据之间分布距离的常用方法。 FID 只是在深度神经网络的特征空间中使用 Wasserstein-2 距离来衡量真实数据和合成数据的接近程度。因此，自然而然地，第一种方法是测试 FID 是否可以解释为什么来自某些生成模型的合成数据比其他模型更有利于学习。特别是，为什么扩散模型比现代 GAN 更有效？

To test this hypothesis, we consider six generative models on cifar10 (five gans and one diffusion model). We first train a robust classifier on 1M synthetic images and measure the performance of real data. As expected, diffusion model synthetic images achieve much higher generalization than other generative models (Table 1). Next, we measure FID of synthetic images from each model. Surprisingly, FID doesn't align with the generalization performance observed when learning from synthetic data. E.g., FID for styleGAN is better than DDPM model while the latter achieves much better generalization performance on real data.

---

为了检验这一假设，我们考虑了 cifar10 上的六个生成模型（五个GAN模型和一个扩散模型）。我们首先在 1M 合成图像上训练一个鲁棒的分类器并测量真实数据的性能。正如预期的那样，扩散模型合成图像比其他生成模型实现了更高的泛化（表 1）。接下来，我们测量每个模型的合成图像的 FID。令人惊讶的是，FID 与从合成数据中学习时观察到的泛化性能不一致。例如，styleGAN 的 FID 优于 DDPM 模型，而后者在真实数据上实现了更好的泛化性能。

Since the goal is to measure distinguishability of two distributions, we try a classification based approach. If synthetic data is indistinguishable from real, then it would be harder to classify them. We test this hypothesis using a binary classifier. However, it turns out that even few layer neural network swere able successfully classify between real and synthetic data of all generative models with near 100% accuracy.

---

由于目标是衡量两个分布的可区分性，我们尝试了一种基于分类的方法。如果合成数据与真实数据无法区分，那么就更难对它们进行分类。我们使用二元分类器检验这个假设。然而，事实证明，即使是几层神经网络也能够以接近 100% 的准确率成功地对所有生成模型的真实数据和合成数据进行分类。

![(a) *Binary classification beween real and synthetic data.* We expand each sample using $\epsilon$-ball, which makes classification success dependent on proximity of synthetic data to real data. (a) 真实数据和合成数据之间的二元分类。我们使用 $\epsilon$-ball 扩展每个样本，这使得分类成功取决于合成数据与真实数据的接近程度。](https://vsehwag.github.io/blog/2022/4/data/arc_motivation.jpeg)

(a) *Binary classification beween real and synthetic data.* We expand each sample using $\epsilon$-ball, which makes classification success dependent on proximity of synthetic data to real data. (a) 真实数据和合成数据之间的二元分类。我们使用 $\epsilon$-ball 扩展每个样本，这使得分类成功取决于合成数据与真实数据的接近程度。

![(b) *ARC.* It refers to the area under the classification success and epsilon( $\epsilon$) curve. Lower ARC score implier higher proximity of synthetic data to real data. (b) ARC 它指的是分类成功和 epsilon( $\epsilon$ ) 曲线下的面积。较低的 ARC 分数意味着合成数据与真实数据的接近度更高。](https://vsehwag.github.io/blog/2022/4/data/arc_curve.png)

(b) *ARC.* It refers to the area under the classification success and epsilon( $\epsilon$) curve. Lower ARC score implier higher proximity of synthetic data to real data. (b) ARC 它指的是分类成功和 epsilon( $\epsilon$ ) 曲线下的面积。较低的 ARC 分数意味着合成数据与真实数据的接近度更高。

- Figure-7. 图 7
    
    When using binary classification as a tool to measure the proximity between synthetic and real data, we encounter an unexpected issue. Even a few layer network successfully classified all synthetic datasets from real. We introduce  $\epsilon$ -balls, i.e., expand each data point using an  $\epsilon$-radius $l_2$ ball around it and ask the classifier to classify these balls correctly. This simple trick makes the classification success dependent on proximity of real and fake data, since lower proximity will lead to balls intersections, thus making classification inpossible. One can then easily derive a metric (we name is ARC) which measures how hard the classification gets with increase in the size of  $\epsilon$-balls.
    
    ---
    
    当使用二元分类作为衡量合成数据和真实数据之间接近程度的工具时，我们遇到了一个意想不到的问题。即使是几层网络也成功地将所有合成数据集与真实数据集进行了分类。我们引入 $\epsilon$ -球，即使用   $\epsilon$-半径 $l_2$ 球围绕它扩展每个数据点，并要求分类器对这些球进行正确分类。这个简单的技巧使分类成功取决于真实数据和假数据的接近程度，因为较低的接近程度会导致球相交，从而使分类变得不可能。然后可以很容易地推导出一个度量（我们命名为 ARC），它衡量随着 $\epsilon$ 球大小的增加分类的难度。
    

So we need to increase the dependence on discriminator success on distance between real and synthetic data distributions. This can be achieve using a very simple tool: $\epsilon$-balls (figure 7.a). We first draw a ball of radius $r$ (it's a hypersphere if we use $l_2$ norm and a hypercube for $l_\infty$ norm) around each data point. Now the objective is to classify all $\epsilon$-balls correctly. If the synthetic and real dataset are in close proximity, drawing a decision boundary between them will become hard with small values of $\epsilon$ itself. We measure the area under the classification success and $\epsilon$ curve (referred to as ARC). ARC effectively measures the distinguishability of synthetic data from real data.

---

因此，我们需要增加对鉴别器成功对真实数据分布和合成数据分布之间距离的依赖。这可以使用一个非常简单的工具来实现： $\epsilon$-balls（图 7.a）。我们首先在每个数据点周围绘制一个半径为 $r$ 的球（如果我们使用 $l_2$ 范数和 $l_\infty$ 范数则为超立方体）。现在的目标是正确分类所有 $\epsilon$-balls。如果合成数据集和真实数据集非常接近，在它们之间绘制决策边界将变得很难，因为 $\epsilon$ 本身的值很小。我们测量分类成功和 $\epsilon$ 曲线下的面积（简称ARC）。 ARC 有效地衡量了合成数据与真实数据的可区分性。

ARC also explain why synthetic data from diffusion models is significantly more helpful than any other generative model. On cifar10 dataset, ARC values for diffusion mode is 0.06, much lower than the best performing GAN (table-2). It also serves as a better metric than FID in predicting generative model success when their synthetic data is used in augment real data.

---

ARC 还解释了为什么来自扩散模型的合成数据比任何其他生成模型更有用。在 cifar10 数据集上，扩散模式的 ARC 值为 0.06，远低于表现最佳的 GAN（表 2）。当它们的合成数据用于增强真实数据时，它在预测生成模型成功方面也比 FID 更好。

![https://vsehwag.github.io/blog/2022/4/data/arc_success.jpeg](https://vsehwag.github.io/blog/2022/4/data/arc_success.jpeg)

- Table-1. **Measuring distribution distance between real and synthetic data with ARC (Limitation of FID).** 表格1 - 使用 ARC（FID的限制）测量真实数据和合成数据之间的分布距离
    
    Our objective is to test whether the distance between real and synthetic datasets can predict the benefit of synthetic data in classification. For the ground truth, we adversarially train a wide-resnet model on one million synthetic images for each generative model and measure its robust accuracy on *real* cifar-10 images. Intuitively, if synthetic data is close to real data then we would expect it to provide higher benefit. But how do we measure proximity of synthetic data to real. FID is the most common metric for this task, where it measures the Wasserstein distance between real and synthetic data distribution in the feature space. However, models with better FID (lower is better) doesn't necessarily provide a better performance boost in learning. E.g., FID for styleGAN is better than diffusion model (ddpm) but the latter achieves better generalization on real data. As a solution, we propose ARC, which successfully explains the benefit of different generative models. Especially it explains why dffision models are much better than others since ARC score for diffusion models is much better than all other models in our study.。
    
    ---
    
    我们的目标是测试真实数据集和合成数据集之间的距离是否可以预测合成数据在分类中的优势。根据真实类别，我们针对每个生成模型在一百万张合成图像上对抗性地训练一个 wide-resnet 模型，并测量其在真实 cifar-10 图像上的鲁棒精度。直觉上，如果合成数据接近真实数据，那么我们会期望它提供更高的收益。但是我们如何衡量合成数据与真实数据的接近程度。 FID 是此任务最常用的指标，它测量特征空间中真实数据分布和合成数据分布之间的 Wasserstein 距离。然而，具有更好 FID（越低越好）的模型并不一定能在学习中提供更好的性能提升。例如，styleGAN 的 FID 优于扩散模型 (ddpm)，但后者在真实数据上实现了更好的泛化。作为解决方案，我们提出了 ARC，它成功地解释了不同生成模型的好处。特别是它解释了为什么 dffision 模型比其他模型好得多，因为扩散模型的 ARC 得分比我们研究中的所有其他模型好得多。
    

## **Discussion**

This post is largely based on my recent work that demonstrates benefit of synthetic data diffusion models in robust learning. The motivation to write it was to discuss the potential and bigger picture of how synthetic data can play a crucial role in deep learning, something that the rigid and scientific writing style of a paper doesn't permit.

---

这篇文章主要基于我最近的工作，该工作展示了合成数据扩散模型在鲁棒学习中的好处。写这篇文章的动机是讨论合成数据如何在深度学习中发挥关键作用的潜力和更广阔的前景，这是一篇严谨而科学的写作风格所不允许的。

> *Robust Learning Meets Generative Models: Can Proxy Distributions Improve Adversarial Robustness?*, Sehwag et al., **ICLR 2022** ([Link](https://arxiv.org/abs/2104.09425))
> 

Diffusion models finally enable the use of synthetic data as a mean to improve representation learning, i.e., move past the inflection point. With further progress in diffusion models, we will likely see higher utility from their synthetic data. However, one doesn't need to limit to using synthetic data as the only approach to integrate diffusion models in representation learning pipeline. In fact, the most important question in the research direction is **how to distill knowledge from diffusion models?** The current setup, i.e., sample synthetic images and use them with real data is a strong baseline, but has two limitations 1) It treats generative models in insolation to discriminative models 2) In addition, the generative models were trained without accounting that the resulted synthetic data will be used for augmentation in classification tasks. A more harmonious integration of both models will likely further improve performance.

---

扩散模型最终能够使用合成数据作为改进表示学习的手段，即越过拐点。随着扩散模型的进一步发展，我们可能会从他们的合成数据中看到更高的效用。然而，不需要限制使用合成数据作为将扩散模型集成到表示学习管道中的唯一方法。事实上，研究方向最重要的问题是如何从扩散模型中提取知识？当前的设置，即对合成图像进行采样并将其与真实数据一起使用是一个强大的基线，但有两个**局限性 1) 它将日照中的生成模型处理为判别模型 2) 此外，生成模型的训练没有考虑结果合成数据将用于增强分类任务。**两种模型的更和谐集成可能会进一步提高性能。

**Adaptive sampling.** Are are all synthetic samples equally beneficial? We touch upon this question in our work [[1](https://arxiv.org/abs/2104.09425)] and show that one can get extra benefit from synthetic data by adaptively selecting samples. However, there is so much that can be done in this direction. Ideally we want to sample synthetic images from low-density regions on data manifold, i.e., regions on the data manifold that are poorly covered by real data.

---

自适应采样。所有合成样本都同样有益吗？我们在我们的工作 [[1](https://arxiv.org/abs/2104.09425)] 中谈到了这个问题，并表明可以**通过自适应选择样本从合成数据中获得额外的好处**。但是，在这个方向上可以做很多事情。理想情况下，我们希望从数据流形上的低密度区域（即数据流形上未被真实数据覆盖的区域）采样合成图像。

**Fine-grained metrics to measure synthetic data quality.** To develop adaptive sampling techniques, we essentially need to build measurement tools to indentify quality of different subgroups of synthetic images. In other words, what we can't measure, we can't understand. Metrics such as FID, Precision-Recall, and ARC only provides a distribution level measure of data quality. We would need to develop metric, or tune existing ones, to cater to sub-groups of our datsets.

**用于衡量合成数据质量的细粒度指标**。为了开发自适应采样技术，我们本质上需要构建测量工具来识别不同合成图像子组的质量。换句话说，我们无法衡量的东西，我们无法理解。 FID、Precision-Recall 和 ARC 等指标仅提供数据质量的分布级别度量。我们需要开发指标或调整现有指标，以迎合数据集的子组。

> Translate by @Yongkang Chen 2023/04/29
>
