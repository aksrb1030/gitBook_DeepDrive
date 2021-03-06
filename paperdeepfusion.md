|논문명 |Deeply-Fused Nets |
| --- | --- |
| 저자\(소속\) | Jingdong Wang \(MS\) |
| 학회/년도 | May 2016, [논문](https://arxiv.org/abs/1605.07716) |
| 키워드 |DeeplyFusion2016, MV3D에 영향줌 |
| 데이터셋(센서)/모델 | CIFAR-10,100 |
| 참고 | |
| 코드 | |

#  DeeplyFusion

중간 representations 을 합치는 기능 수행, 합쳐진 결과는 네트워크의 나머지 부분의 입력으로 작용 
- **Combine** the **intermediate representations** of base networks, 
	- where the **fused output** serves as the **input** of the remaining part of each base network, 
	- and perform such combinations deeply over **several intermediate representations**

제안 알고리즘의 장점 
1. 멀티 스캐일 표현들을 학습 할수 있음 `it is able to learn multi-scale representations as it enjoys the benefits of more base networks, `
	- which could form the same fused network, other than the initial group of base networks. 
2. 성능 개선, Second, in our suggested fused net formed by **one deep** and one **shallow base networks**
	- the flows of the information from the earlier intermediate layer of the deep base network to the output and from the input to the later intermediate layer of the deep base network are **both improved**. 
3. 성능 개선, Last, the deep and shallow base networks are **jointly learnt** and can **benefit from each other**. 

특징 
- 사용된 두 네트워크(Deep & Shallow) 모두 깊이가 줄어듬 the essential depth of a fused net composed from a deep base network and a shallow base network is **reduced**
- 왜냐 하면.. because the fused net could be composed from a less deep base network, 
- 덕분에 ....and thus training the fused net is less difficult than training the initial deep base network.

## 1 Introduction

딥러닝은 최근 많은 발전을 보였지만 아직 효율성은 부족하다. 효율성 증대를 위한 다양한 연구가 있따. 

- over-fitting 예방 :  
	- Dropout [8] and 
	- other regularization techniques, such as **weight decay** and **path regularization** [9]
- vanishing gradient problem : 
	- Normalized variance-preserving weight initialization, such as [10–12], 
- both the training speed and the recognition performance :
	-  Batch normalization [13] 
- improve the flow of information and accordingly help train a very deep network. : 
	- Skip-layer connections between layers (including the output layer) and 
	- other network structure modifications, such as **deeply-supervised nets** [14] and its variant [7], Highway [15], ResNet [16], inception-v4 [3]

본 논문의 제안 
- **Base networks 그룹**으로 구성된 deeply-fused neural net 제안`we introduce a deep fusion approach and present a deeply-fused neural net formed by combining a group of base networks`

- 핵심은 기초 네트워크의 중간 representations 들을 퓨전 하는 것이다. 이때의 결과물은 다음 기초 네트워크의 입력으로 활용된다. `The main idea is to perform fusion over the intermediate representations of the base networks, where the fused output serves as the input of the remaining part of each base network, rather than only over the final representations or the final classification scores,and such fusions are performed several times at different intermediate layers.`

- There is a block-exchangeable property `(the block is the subnetwork between two successive fusions in a base network)`:
	-  switch blocks from one base network to another one within one fusion, 
	- resulting in two different base networks with possibly different depth from the originals `(e.g., deep network being less deep and shallow network being less shallow)`, but the fused net is not changed. 

- 다르게 보면 **fused net**은 서로 다른 기초 네트워크의 그룹으로 구성 할수 있다. `In other words, a fused net can be formed by different groups of base networks. `
	- 덕분에 Thus,the deeply-fused net is able to learn multi-scale representations from much more base networks, and even same-scale representations can be different and learnt from different base networks.

- 또 다른 장점은 정보의 흐름이 개선(`칼만필터 효과??`) 된다는 것이다. `There is one more benefit from deep fusion: the flow of information is improved.`
	- Consider the case where one base network is very deep but the other base network is not deep, which is the choice we suggest. 

The earlier intermediate layer in the deeper base network might have a shorter path through the other base network to the output, which implies that the supervision can be fast transformed to the earlier intermediate layer. 

On the other hand, the later intermediatelayer might also have a shorter path from the input, which indicatesthat the input can be fast flowed to the later intermediate layer. 

As a result,training the fused net composed from a very deep base network is less difficultthan training the very deep base network itself. 

Furthermore, the deep and shallowbase networks are jointly learnt and can benefit from each other. 

## 2 Related Work

we mainly discuss two closely-related lines: 
- network structure design 
- network optimization with the aid of another already trained network.

- 예측치의 **평균값**을 쓰는것은 일반화된 정확도의 향상을 가져 오고 많이 사용 되었다. `Averaging over a set of network predictors, (which we call decision fusion), is able to improve the generalization accuracy and has been widely used, e.g., to boost the ImageNet recognition performance [1, 16, 19, 20]. `

```
1. Krizhevsky, A., Sutskever, I., Hinton, G.E.: Imagenet classification with deep convolutional neural networks. In: Advances in Neural Information Processing Systems 25: 26th Annual Conference on Neural Information Processing Systems 2012. Proceedings of a meeting held December 3-6, 2012, Lake Tahoe, Nevada, United States. (2012) 1106–1114
16. He, K., Zhang, X., Ren, S., Sun, J.: Deep residual learning for image recognition. CoRR abs/1512.03385 (2015)
19. Simonyan, K., Zisserman, A.: Very deep convolutional networks for large-scale image recognition. CoRR abs/1409.1556 (2014)
20. Szegedy, C., Liu, W., Jia, Y., Sermanet, P., Reed, S., Anguelov, D., Erhan, D.,Vanhoucke, V., Rabinovich, A.: Going deeper with convolutions. In: IEEE Conference on Computer Vision and Pattern Recognition, CVPR 2015, Boston, MA, USA, June 7-12, 2015. (2015) 1–9
```

- 입력에 따라 **평균 가중치(weighted averaging)**를 고려하는 방식이 이후 제안 되었다. `Multi-column deep neural networks [21] presents an empirical study about decision fusion, later extended to an adaptive version, weighted averaging with the weights depending on the input [22]. `

- 평균`(averaging )`을 이용한 방식은 각 네트워크를 독립적으로 학습 한다. `The averaging approach learns each network separately, which is equivalent to learn the network jointly that averages the loss functions. `

- 반면 제안 방식은 특징들을 fusion한 후에 동시에 학습하는 방법이다. `Our approach, in contrast, performs the feature fusion deeply over several intermediate layers and simultaneously learns the representations of the (base) networks.`

```
21. Ciresan, D.C., Meier, U., Schmidhuber, J.: Multi-column deep neural networks for image classification. In: 2012 IEEE CVPR
22. Agostinelli, F., Anderson, M.R., Lee, H.: Robust image denoising with multicolumn deep neural networks. 2013.
```

###### [GooLeNet과의 비교]

- GooLeNet의 Inception도 퓨전 방식으로 볼수 있다. `The inception module in GoogLeNet [20] can be viewed as a fusion stage: concatenate the outputs of several subnetworks with different lengths. `
	- 다른점은 제안 방식은 퓨전의 합(sum)을 이용하는 것이다. `It is different from our approach using the summation for fusion. `
	- The GoogLeNet architecture, `consisting of a sequence of inception modules`, is also a kind of deep fusion, 
		- i.e.,deep concatenation fusion. 

But it is not as direct as our deep summation fusion.

The output of each subnetwork in an inception module is narrower than the input of the subsequent inception module. 

Hence it is necessary to append many channels with all 0 entries in the output to match the size with the input of the subsequent inception module or add more convolution operations to form the fused network. 

###### [Skip-layer connection와의  비교]

- Skip-layer connection resembles our approach and can be regarded as special examples of our approach.

> Skip-layer connection = deeply-supervised nets [14],  its variant [7], Highway [15], ResNet [16], 

```
14. Lee, C., Xie, S., Gallagher, P.W., Zhang, Z., Tu, Z.: Deeply-supervised nets. In:Proceedings of the Eighteenth International Conference on Artificial Intelligence and Statistics, AISTATS 2015, San Diego, California, USA, May 9-12, 2015. (2015)
7. Xie, S., Tu, Z.: Holistically-nested edge detection. In: 2015 IEEE International Conference on Computer Vision, ICCV 2015, Santiago, Chile, December 7-13, 2015. (2015) 1395–1403
15. Srivastava, R.K., Greff, K., Schmidhuber, J.: Training very deep networks. CoRR abs/1507.06228 (2015)
16. He, K., Zhang, X., Ren, S., Sun, J.: Deep residual learning for image recognition.CoRR abs/1512.03385 (2015)
```

###### [teacher-student framework와의 비교]

- The teacher-student framework suggests that 
	- learning a hard-trained network can benefit from an easily-trained network. 

For instance, 
- FitNets [17] uses the intermediate representation of a wider and shallower (but still deep) teacher-net that is relatively easy to be trained, as the target of the intermediate representation of a thinner and deeper student net. 

- Net2Net [18] also uses a teacher-net to help train a (wider or deeper) student net, through a function-preserving transform to initialize the parameters of the student net according to the parameters of the teacher net. 

```
17. Romero, A., Ballas, N., Kahou, S.E., Chassang, A., Gatta, C., Bengio, Y.: Fitnets: Hints for thin deep nets. CoRR abs/1412.6550 (2014)
18. Chen, T., Goodfellow, I.J., Shlens, J.: Net2net: Accelerating learning via knowledge transfer. CoRR abs/1511.05641 (2015)
```
제안 방식은 Our approach, in our suggested choice: including one deep base network and one shallow (but could still be deep) network, also uses the shallow network to help train the deep base network, meanwhile the deep base network also helps train the shallow network, i.e., they benefit from each other and are trained simultaneously.

## 3. 제안 방식 
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTg3MTAyOTUzNV19
-->