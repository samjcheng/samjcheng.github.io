            
            
# Reviewer UYwZ

We thank the reviewer for the detailed comments which are useful for us to improve our paper. 
#### Q1: I am curious about the choice of edge information and loss function. 
Reply: The different edge information does not affect much the results. We compared Canny edge with ideal ground truth edge in SceneFlow and the results are comparable. In addition, we have also reported the use of key-points (SuperPoint) and obtained similar results. 
|Method| RTNet |PSMNet |GwcNet |ACVNet
---- | ---- |---- |---- |---- 
Canny Edge | 3.265 |0.644 |0.650 |0.449
Ground Truth Edge| 3.200 |0.649 |0.641| 0.448
SuperPoint |3.191 |x |x |x
 
#### Q2: Do the parameters of edge information affect the performance of stereo matching?
Reply: We use cv.Canny() from OpenCV with recommended automatic thresholds (lower and upper) computed from the image. We have also test RTNet+SPR and PSMnet+SPR using different thresholds: (lower, upper) = (20, 200), (60, 200), (100, 200), (100, 120), (100, 160). Our results show that the performances do not change much. 
|Methods | (20,200) | (60,200) | (100, 200) |  (100, 160)  | (100, 120)   | Auto 
---- | ---- |---- |---- |---- | ----|---- 
RTNet+SPR|  3.258 | 3.248 | 3.236  | 3.229| 3.233 | 3.265
PSMNet+SPR|  x | x| x| x| x|  0.644
 
            
#### Q3: I hope the authors can provide some in-depth analysis of the edge information in the stereo matching. 
Reply: In our methods, we are NOT using edge to guide the computation of disparity map. Instead, we are using disparity map to guide the edge detection. In such a way, we are actually imposing a constraint on disparity maps such that it is in favour of edge detection. It would encourage a smooth disparity for a region with homogeneous RGB colours, otherwise, it may cause artefacts in edge detection. Therefore, we would expect to have smooth disparity map for region without much RGB changes. 

#### Q4: What is the generalization ability when testing across datasets?
Reply: We have also tested the cross-dataset generalization. We test our models on Middlebury datasets, the results show that the cross-dataset performances are also improved. The below table shows the EPE for comparison. 
 |Lower | KITTI 2012 | KITTI 2015 | Middlebury
---- | ---- |---- |---- 
RTNet | 5.08 | 4.74 | 5.53 
RTNet+SPR  | 4.72 | 4.30| 4.76
PSMNet  | 3.51 | 4.00| 3.91
PSMNet+SPR  | 2.90 | 3.97| 3.49
GwcNet | 1.61| 2.35| 1.95
Gwcet+SPR  | x | x| x
ACVNet  | 1.85| 2.44| 2.15
ACVNet+SPR | 1.64 | 2.22| 1.78

#### L1: I don't think the canny edge information is a physical information. It would be better to use low-level information instead of physical information.
Reply: We thank the reviewer for the suggestion and we would modify and highlight it as low-level information regularization. 
    
#### L2: The motivation of this paper is somewhat the same as EdgeStereo. It reduces the impact of this work.
Reply: Although both approaches use edge, there is a major difference. EdgeStereo cooperates edge cues into disparity estimation pipeline and use edge to guide  guides disparity learning with edge-aware smoothness loss. However, our method use disparity map to guide edge detection. The advantage of our method is two-fold: 1) our method can be easily applied to almost any existing methods without more computation in inference while EdgeStereo requires. 2) When edge detection is used to guide stereo matching, it might lead to artefacts in disparity estimation. Moreover, we find that key-points work as good as edges in our framework, which was not discussed in EdgeStereo.
            




# Reviewer  SXrn
            
#### Question 1: Key-point based regularization - I am a little confused from the paper - which experiments include the key-points as the "physical regularization" and which include the Canny edges? do some experiments include both?
Reply: In Table 3 of our manuscript, the third row “Super Point” is a case we use key-point as physical regularization. The rest of results in Table 3 use “Canny Edges”. The ablation study in Table 3 is to show that the aggregation module is effective and that the physical regularization (or low-level structure regularization) using Canny Edge or key-points are effective. Here we provide more results when we use key-points under different network structures (Concat, EA, SA and our proposed aggregation).     
           
Methods|RTNet |PSMNet 
---- | ---- |---- 
Baseline|     3.38 | 1.09
Multi-task(SuperPoint)|  3.28|  0.70
Proposed(SuperPoint)|  3.19 | x
 
            
#### Question 2: Address the two limitations stated below
#### 2.1:	Failure cases - in what cases is this approach not beneficial? Specifically, why did the method decrease the results in cases like: The 3-noc and 3- all for GwcNet in KITTI 2012; The D1-fg for GwcNet in KITTI 2015

Reply : Our method is motivated by the observation that sudden change in disparity often leads to changes in RGB images. As our regularization is achieved through the detection of low-level structures such as edge, it actually encourage the model to compute a dispairty map that is in favor of edge detection. In another word, we expect to obtain little or smooth change of disparity for regions without edges or keypoints. Our method would not be benefical when this assuption is no longer valid, i.e., we have large disparity changes but there is no obvious edge in corresponding RGB images. Such scenario might happen, for example,  we have two neighboring objects with same colors (and alsk of textures) but at different distance/depth. 

Our approach essentially changes the convergence of the model by imposing the regularization term. However, there is no guarantee that the new converged model is always better than previous one without the regularization. Therefore, there exist cases where the new model is similar to previous model if the previous model is not over-fitted or the over-fitting is not serious. This is also a reason that the improvement in GwcNet happen to be smaller and even drop slightly for KITTI 2012/2015 with only less than 200 training pairs. 


            

#### 2.2: Justification for the "disparity aggregation" block in the paper. Specifically, is there a gradual increase of complexity from option (a) to (e) in Figure 2? For example: "option (a) is the most straightforward way to make a stub-loss, (b)+(c) are non-stub (why?), and the last two are based on concatenation". Specifically, the "jump" from (d) to the selected method (e) is not clear to me. I would advise to elaborate here.

Reply: We thank the reviewer for raising this question, which helps us to clarify our motivation and experimental design better. Option (a) is the most-straightforward where we want to show that a simple multi-task learning would improve the performance. However, as there exists some correlation (a sudden change in disparity maps may indicate an edge in RGB images but not vice versa ) between the disparity map and the edge, we want to investigate if we can make use of this observation to further improve the results. The design (b) is to add the “edge” into disparity estimation, this is similar to some earlier methods such as EdgeStereo where edges in RGB are used to guide or smooth the disparity maps. This actually lead to artefact or unnatural edge in disparity map, which is also a limitation of EdgeStereo. Option (d) is a case where disparity maps are used to guide edge detection via a simple concatenation. The comparison between (d) and (e) is to show that an aggregation between disparity maps and edge features (feature to compute edges) further improves the results comapred with simple concatenation. The option (c) is to make use mutual guidance between edge detection and disparity estimation. From these studies, we want to show quantitatively that using edges to guide the disparity estimation can damage the performance compared with the proposed method which uses disparity map to guide the edge detection. Our further results suggest that this observation still stands if we change “edges” to key-point. 
 

 

# Reviewer EgaM
We thank the reviewer for the effort to review our work. However, we do not agree with the comments that “proposing a regularization is not that good an advancement” and that stereo matching is “an already solved problem”. In fact, our results show that imposing a regularization in the training stage can improve the results by as large as 41.3% in some situation such as PSMNet. 

The reviewer has suggested us to compare with the algorithms from the leaderboard of KITTI 2015. However, some reported results from the leaderboard do not come with codes and are unpublished. In our paper, we choose four different published peer-reviewed algorithms to justify the generality of our method. The RTNet is chosen as a representative algorithm for edge side deployment. PSMNet is one of the early attempt to use deep learning for stereo matching. GwcNet and ACVNet are from recent publications where ACVNet is the best among peer reviewed papers. To date, ACVNet still outperforms most existing published/peer reviewed methods with D1-all 1.65%. It is the second score just after the new accepted CVPR 2023 paper IGEV-Stereo with D1-all 1.59%.    
            
### Q1: What is the motivation to propose this small tweak to major networks for this problem?
Reply: Our work is inspired from the fact that the purely data-driven model may lead to a representation that is often locally optimal and biased to the training data. By introducing additional constraints, we expect to change the model’s convergence. As we mentioned in our paper that fusing model-based approaches with data-driven approaches help to reduce overfitting, our work is an attempt in stereo matching and one more example in this direction. 
            
### Q2: Why there is no change in the network from scratch?
Reply: We try not to change the network at inference from two reasons. The first is that it makes the regularization term be easily applied to almost any existing methods without extra computational cost, which is important for deployment at edge side where extra computation is unaffordable.  The second reason is that our experimental results show that current design works better than other forms of regularization in Figure 2. Moreover, when the output of the regularization term is used to guide the disparity map, there is a concern that the edge information may lead to some artifact in the disparity map. Our method uses disparity map to guide the edge estimation and reduce such risks.



            
            
