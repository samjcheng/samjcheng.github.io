            
            
# Reviewer UYwZ

We thank the reviewer for the detailed comments which are useful for us to improve our paper. We appreciate your careful reading and insightful advices. We hope to address your concerns with additional experiments and analysis.
            
#### Question 1: I am curious about the choice of edge information and loss function. Does any other low-level information work for stereo matching?
Reply 1: The choice of different edge information in the regularization term does not affect much the performance improvement. Actually, we compared Canny edge with ideal ground truth edge (computed from the annotation) in Scene Flow dataset and our results show that the performance improvements are comparable. The results for four different methods are given in Table 3 in the original submission and copied here.  We further applied Sobel edge, the results are similar. Please noted that when key-points (using SuperPoint) are used, we also get similar improvement. 

|Method| RTNet |PSMNet |GwcNet |ACVNet
---- | ---- |---- |---- |---- 
Canny Edge | 3.265 |0.644 |0.650 |0.449
Ground Truth Edge| 3.200 |0.649 |0.641| 0.448
Sobel Edge |  | | \- | \-

#### Question 2: Do the parameters of edge information affect the performance of stereo matching?
Reply 2:  In our implementation, we use cv.Canny(I, lower, upper) from OpenCV with automatical thresholds: 
lower = (1 – sigma)\*v, upper =(1+sigma)\*v, where sigma is set at 0.33 by default and v is the median of the image I. We have also test our algorithms using different thresholds: (lower, upper) = (20, 200), (60, 200), (100, 200), (100, 120), (100, 160). Our results show that the performance does not change much for reasonable thresholds. 

|Methods | (20,200) | (60,200) | (100, 200) |  (100, 160)  | (100, 120)   | Auto 
---- | ---- |---- |---- |---- | ----|---- 
RTNet+SPR|  3.258 | 3.248 | 3.  | 3.229| 3.| 3.265
PSMNet+SPR|  0.44 | 0.45| 0.46| 0.48| 0.49| 0.5  
 
            
#### Question 3: I hope the authors can provide some in-depth analysis of the edge information in the stereo matching. For example, the edge information can correct the boundary of the disparity map? or make the non-edge region more smooth?
Reply 3: In our methods (option (d) and (e) in Figure 2), we are NOT using edge to guide the computation of disparity map or smooth it. Instead, we are using disparity map to guide the edge detection. As disparity map is combined (via concatenation or via disparity aggregation) to feature maps for the edge (or low-level structure) detection branch, we are actually imposing an constraint on disparity maps such that it is infavor of edge detection. As the disparity map plays some role (weighted) in edge detection, it can be expected that if the disparity maps is not smooth in a region with homogenious RGB colors, it may causes artefacts in edge detection. Therefore, we would expect to have smooth dispairty map for region without much RGB color changes. 

#### Question 4: What is the generalization ability when testing across datasets?
Reply 4: We have also tested the cross-dataset generalization. We test our models trained from Sceneflow datasets and tested on ETH3D and Mxxx datasets, the results show that the cross-dataset performances are also improved by the regularization. The below table shows the end-to-end point error (EPE) for RTNet and PSMNet as well as their results after regularized by our proposed SPR method. 
 |Lower |Metrics | Middleburry | KITTI 2012 | KITTI 2015
---- | ---- |---- |---- | ----
RTNet|EPE  | 0.44 | 0.45| 0.46 
RTNet+SPR|EPE  | 0.44 | 0.45| 0.46
PSMNet|EPE  | 0.44 | 0.45| 0.46
PSMNet+SPR|EPE  | 0.44 | 0.45| 0.46
GwcNet|EPE  | 0.44 | 0.45| 0.46
Gwcet+SPR|EPE  | 0.44 | 0.45| 0.46
ACVNet|EPE  | 0.44 | 0.45| 0.46
ACVNet+SPR|EPE  | 0.44 | 0.45| 0.46
            

            
# Reviewer EgaM
We thank the reviewer for the time and effort to review our work. However, we do not agree with the comments that “proposing a regularization is not that good an advancement” and the comments that stereo matching is “an already solved problem”. In fact, our results show that we can improve the trained models by imposing a regularization in the training stage without changing its architecture in inference stage. In some situation, the improvement can be as large as 41.3% (e.g, EPE droped from 1.09 to 0.64 in PSMNet). 

The reviewer also suggested us to compare with the algorithms from the leaderboard of KITTI 2015. However, some reported results from the leaderboard do not come with codes and are unpublished. It is therefore challenge for us the compare with these algorithms. In our paper, we choose four different published algorithms from different stages/objectives to justify the genearlity of our method. The RTNet is chosen as it is a representative algorithm designed for edge side deployment. PSMNet is chosen as it is one of early attemp to use deep learning for stereo matching. GwcNet and ACVNet are chosen as they are from recent publications. In particular, ACVNet outperforms most existing published methods and ranks No.2 in KITTI 2012 and KITTI 2015 leaderboards at the time of publication of the paper. It is worth mentioning that the ACVNet is also the fastest among the top 10 methods in the KITTI benchmark leaderboards. Therefore, we chose ACVNet as one of most competetive algorthms.  
            
#### Question 1: What is the motivation to propose this small tweak to major networks for this problem?
Reply 1: Our motivation to impose a regularization inspired from the fact that the purely data-driven model optimized for stereo matching may lead to a representation that is often locally optimal and biased to the training data. By introducing additional constraints from low-level information, we expect to change the convergence of the models. Our experimental results suggest that we are able to obtain some improvement when combined with different baseline approaches. 
            
#### Question 2: Why there is no change in the network from scratch?
Reply 2: We try not to change the network of existing methods from two reasons. The first reason is that the regularization term can be easily applied to almost any existing methods without extra computational time in inference stage. This is actually very important as the models may need to be deployed at edge side with limited computational resources.  The second reason is that our experimental results show that current design works better than other forms of regularization. Actually, if the output of the regularization branch is further used by the disparity estimation branch as in option (b) or (c), we obtained lower performance compared our design. Moreover, when the output of the regularization term (take the edge as an example), there is a concern that the edge information may lead to some artifact in the disparity map. 



# Reviewer  SXrn
            
#### Question 1: Key-point based regularization - I am a little confused from the paper - which experiments include the key-points as the "physical regularization" and which include the Canny edges? do some experiments include both?
Reply 1: In Table 3 of our manuscript, the third row “Super Point” is a case we use key-point as physical regularization. The rest of results in Table 3 use “Canny Edges”. The ablation study in Table 3 is to show that the aggregation module is effective and that the physical regularization (or low-level structure regularization) using Canny Edge or key-points are effective. Here we provide more results when we use key-points under different network structures (Concat, EA, SA and our proposed aggregation).     
           
Methods|RTNet |PSMNet| GwcNet | ACVNet  
---- | ---- |---- |---- |----  
Baseline|     0.44 | 0.45| 0.46 | 0.46
Multi-task|  0.44 | 0.45| 0.46 | 0.46
Concat|  0.44 | 0.45| 0.46 | 0.46
EA|   0.44 | 0.45| 0.46 | 0.46
SA | 0.44 | 0.45| 0.46 | 0.46
Proposed|  0.44 | 0.45| 0.46 | 0.46
            
#### Question 2: Address the two limitations stated below
#### 2.1:	Failure cases - in what cases is this approach not beneficial? Specifically, why did the method decrease the results in cases like: The 3-noc and 3- all for GwcNet in KITTI 2012; The D1-fg for GwcNet in KITTI 2015

Reply 2.1 : Our approach essentially changes the convergence of the model by imposing the regularization term. However, there is no guarantee that the new converged model is always better than previous one without the regularization. Therefore, there exist cases where the new model is similar to previous model if the previous model is not over-fitted or the over-fitting is not serious. This is also a reason that the improvement in GwcNet happen to be smaller and even drop slightly. 

Our method is motivated by the observation that sudden change in disparity often leads to changes in RGB images. As our regularization is achieved through the detection of low-level structures such as edge, it actually encourage the model to compute a dispairty map that is in favor of edge detection. In another word, we expect to obtain little or smooth change of disparity for regions without edges or keypoints. A potential failure case is when this assuption is no longer valid, i.e., we have large disparity changes but there is no obvious edge in corresponding RGB images. Such scenario might happen, for example,  we have two neighboring objects with same colors but at different distance/depth.   
            

#### 2.2: Justification for the "disparity aggregation" block in the paper. Specifically, is there a gradual increase of complexity from option (a) to (e) in Figure 2? For example: "option (a) is the most straightforward way to make a stub-loss, (b)+(c) are non-stub (why?), and the last two are based on concatenation". Specifically, the "jump" from (d) to the selected method (e) is not clear to me. I would advise to elaborate here.

Reply 2.2 : We thank the reviewer for raising this question, which helps us to clarify our motivation and experimental design better. Option (a) is the most-straightforward where we want to show that a simple multi-task learning would improve the performance. However, as there exists some correlation (a sudden change in disparity maps may indicate an edge in RGB images but not vice versa ) between the disparity map and the edge, we want to investigate if we can make use of this observation to further improve the results. The design (b) is to add the “edge” into disparity estimation, this is similar to some earlier methods such as EdgeStereo where edges in RGB are used to guide or smooth the disparity maps. This actually lead to artefact or unnatural edge in disparity map, which is also a limitation of EdgeStereo. Option (d) is a case where disparity maps are used to guide edge detection via a simple concatenation. The comparison between (d) and (e) is to show that an aggregation between disparity maps and edge features (feature to compute edges) further improves the results comapred with simple concatenation. The option (c) is to make use mutual guidance between edge detection and disparity estimation. From these studies, we want to show quantitatively that using edges to guide the disparity estimation can damage the performance compared with the proposed method which uses disparity map to guide the edge detection. Our further results suggest that this observation still stands if we change “edges” to key-point. 
 

 





            
            
