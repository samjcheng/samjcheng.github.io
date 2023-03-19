General Responses

We thank the reviewers for the comments and the efforts. It is glad to see that most of reviewers recognizing the values of the work. We have provided our replies to the questions raised by each reviewer in the rebuttals to each review. 

To show generalization ability cross different datasets, we have tested our models pretrained from SceneFlow dataset on KITTI 2012 (training set), KITTI 2015 (training set) and Middlebury for four baseline methods. The results show that our methods also improve the performance in cross-dataset tests.

To evaluate the performance changes of different thresholds in Canny edge detector, we applied different threshold combinations and the results show that the performances are not sensitive to thresholds changes. To show the performance of other regularization, we also show and highlight the performances when keypoints (SuperPoint) is used as regularization instead of Canny edge. Our results show that the performance is comparable. 

We would like to highlight our motivation and the differences from previous methods such as EdgeStereo.  Although both approaches use edge, there is a major difference in the motivation, and rationale behind it. First, we argue that the previous purely-data driven models can be biased while the proposed regularization can alleviate this issue. This is not discussed in EdgeStereo. Second, the use of correlation between edge and disparity map is different. EdgeStereo cooperates edge cues into disparity estimation pipeline and use edge to guide disparity learning with edge-aware smoothness loss. However, our method (the disparity aggregation) use disparity map to guide edge detection. The advantage of our method is two-fold: 1) it can be easily applied to almost any existing methods without more computation in inference while EdgeStereo requires. This is very important as stereo matching might be used in edge side where the computational resources are limited for extra computation.   2) When edge detection is used to guide stereo matching, it might lead to artefacts in disparity estimation. Moreover, we also find that keypoints work as good as edges in our framework, which was not discussed in EdgeStereo as well.

            
# Reviewer UYwZ

We thank the reviewer for the detailed comments which are useful for us to improve our paper. 
#### Q1: I am curious about the choice of edge information and loss function. 
Reply: The different edge information does not affect much the results. We compared Canny edge with ideal ground truth edge in SceneFlow and the results are comparable. In addition, we have also tested the use of key-points (SuperPoint) and obtained similar results. 
|Method| RTNet |PSMNet |GwcNet |ACVNet
---- | ---- |---- |---- |---- 
Canny Edge | 3.265 |0.644 |0.650 |0.449
Ground Truth Edge| 3.200 |0.649 |0.641| 0.448
SuperPoint |3.191 |0.646 |0.647 |0.456
 
#### Q2: Do the parameters of edge information affect the performance of stereo matching?
Reply: We use cv.Canny() from OpenCV with recommended automatic thresholds (lower and upper) computed from the image. We have also test RTNet+SPR and PSMnet+SPR using different thresholds: (lower, upper) =  (60, 200), (100, 200), (100, 120), (100, 160). Our results show that the performances do not change much. 
|Methods | (60,200) | (100, 200) |  (100, 160)  | (100, 120)   | Auto 
---- |---- |---- |---- | ----|---- 
RTNet+SPR| 3.248 | 3.236  | 3.229| 3.233 | 3.265
PSMNet+SPR | 0.623| 0.624 | 0.655| 0.649|  0.644 

 
            
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
GwcNet+SPR  | 1.67 | 2.32| 1.91
ACVNet  | 1.85| 2.44| 2.15
ACVNet+SPR | 1.64 | 2.22| 1.78

#### L1: I don't think the canny edge information is a physical information. It would be better to use low-level information instead of physical information.
Reply: We thank the reviewer for the suggestion and we would modify and highlight it as low-level information regularization. 
    
#### L2: The motivation of this paper is somewhat the same as EdgeStereo. It reduces the impact of this work.
Reply: Although both approaches use edge, there is a major difference. EdgeStereo cooperates edge cues into disparity estimation pipeline and use edge to guide  guides disparity learning with edge-aware smoothness loss. However, our method use disparity map to guide edge detection. The advantage of our method is two-fold: 1) our method can be easily applied to almost any existing methods without more computation in inference while EdgeStereo requires. 2) When edge detection is used to guide stereo matching, it might lead to artefacts in disparity estimation. Moreover, we find that key-points work as good as edges in our framework, which was not discussed in EdgeStereo.
            
# Reviewer EgaM
We thank the reviewer for the effort to review our work. However, we do not agree with the comments that “proposing a regularization is not that good an advancement” and that stereo matching is “an already solved problem”. In fact, our results show that imposing a regularization in the training stage can improve the results by as large as 41.3% in some situation such as PSMNet. 

The reviewer has suggested us to compare with the algorithms from the leaderboard of KITTI 2015. However, some reported results from the leaderboard do not come with codes and are unpublished. In our paper, we choose four different published peer-reviewed algorithms to justify the generality of our method. The RTNet is chosen as a representative algorithm for edge side deployment. PSMNet is one of the early attempt to use deep learning for stereo matching. GwcNet and ACVNet are from recent publications where ACVNet is the best among peer reviewed papers. To date, ACVNet still outperforms most existing published/peer reviewed methods with D1-all 1.65%. It is the second score just after the new accepted CVPR 2023 paper IGEV-Stereo with D1-all 1.59%.    
            
### Q1: What is the motivation to propose this small tweak to major networks for this problem?
Reply: Our work is inspired from the fact that the purely data-driven model may lead to a representation that is often locally optimal and biased to the training data. By introducing additional constraints, we expect to change the model’s convergence. As we mentioned in our paper that fusing model-based approaches with data-driven approaches help to reduce overfitting, our work is an attempt in stereo matching and one more example in this direction. 
            
### Q2: Why there is no change in the network from scratch?
Reply: We try not to change the network at inference from two reasons. The first is that it makes the regularization term be easily applied to almost any existing methods without extra computational cost, which is important for deployment at edge side where extra computation is unaffordable.  The second reason is that our experimental results show that current design works better than other forms of regularization in Figure 2. Moreover, when the output of the regularization term is used to guide the disparity map, there is a concern that the edge information may lead to some artifact in the disparity map. Our method uses disparity map to guide the edge estimation and reduce such risks.





# Reviewer  SXrn
            
### Q1: Key-point based regularization - I am a little confused from the paper - which experiments include the key-points as the "physical regularization" and which include the Canny edges? do some experiments include both?
Reply: The row “SuperPoint” In Table 3 uses keypoints via multitask while the rest uses edges. We provide more results using keypoints below. We did not find further benefit to use both keypoints and edges in our experiments.   
           
Methods|RTNet |PSMNet 
---- | ---- |---- 
Baseline|     3.90 | 1.09
Multi-task(SuperPoint)|  3.28|  0.70
Proposed(SuperPoint)|  3.19 | 0.65
            
### Q2: Address the two limitations stated below
#### 2.1: Failure cases 
Reply : Our method essentially changes the convergence of the model. However, there is no guarantee that the new model is always better. Therefore, there exist cases where the new model is similar to previous ones if the bias of the previous model is not serious. This is also a reason that the performance in GwcNet is not improved or even slightly decreased in some datasets. The proposed disparity aggregation module is motivated by the observation that sudden change in disparity often leads to changes in RGB images but not vice versa. It would encourage the model to compute a smooth disparity for non-edge regions. It would not be beneficial if this assumption is not valid, i.e., we have large disparity changes but no obvious edge in corresponding RGB images. Such scenario might happen, e.g., two neighboring objects with same colours in imaging but at different distance/depth. 
           
#### 2.2: Justification for the "disparity aggregation" block in the paper.  
Reply: We thank the reviewer for raising this question, which helps us to clarify our motivation. Option (a) is to show that a simple multi-task learning improves the performance. However, as there exists a correlation between the disparity map and the edge, we want to explore to further improve the results from this correlation. The design (b) uses edge to guide disparity estimation, similar to that in EdgeStereo. This actually led to a concern on potential artefact or unnatural edge in disparity map. The option (c) is to make use of mutual guidance between edge detection and disparity estimation. Option (d) is uses disparity map to guide edge detection via a simple concatenation. Option (e) further justify the benefits of the aggregation module compared with (d). Our results show (e) achieves best result.

 

 



