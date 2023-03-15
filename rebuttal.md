            
|Methods|Metrics |20 | 40 | 60 | 80 | 100  
---- | ---- |---- |---- |---- | ----|---- 
RTNet|<p> EPE <p> D1  | 0.44 | 0.45| 0.46| 0.48| 0.49| 0.5 
RTNet+SPR|<p> EPE <p> D1 | 0.44 | 0.45| 0.46| 0.48| 0.49| 0.5  
PSMNet|<p> EPE <p> D1 | 0.44 | 0.45| 0.46| 0.48| 0.49| 0.5 
PSMNet+SPR|<p> EPE <p> D1  | 0.44 | 0.45| 0.46| 0.48| 0.49| 0.5  
GwcNet|EPE  | 0.44 | 0.45| 0.46| 0.48| 0.49| 0.5 
Gwcet+SPR|<p> EPE <p> D1  | 0.44 | 0.45| 0.46| 0.48| 0.49| 0.5  
ACVNet|EPE  | 0.44 | 0.45| 0.46| 0.48| 0.49| 0.5 
ACVNet+SPR|<p> EPE <p> D1  | 0.44 | 0.45| 0.46| 0.48| 0.49| 0.5  



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

We do not agree with the comments that “proposing a regularization is not that good an advancement” and the comments that stereo matching is “an already solved problem”. In fact, our results show that we can improve the trained model by imposing a regularization in the training stage without changing its architecture in inference stage. In some situation, th
e improvement can be as large as xxx%. 
Questions: What is the motivation to propose this small tweak to major networks for this problem?
Our motivation to impose a regularization is inspired from the purely data-driven model optimized for stereo matching may lead to a representation that is often locally optimal and biased to the training data.  
Why there is no change in the network from scratch?
 We try not to change the network of existing methods from two reasons. The first reason is that with this no change property, the regularization term   can be easily applied to almost any existing methods. The second reason is that our experimental results show that current design and use of the regularization works better than other forms. Actually, if the output of the regularization branch is further used by the disparity estimation branch, the performances drop according to our results. Moreover, when the output of the regularization term (take the edge as an example), there is a concern that the edge information may lead to some artifact in the disparity map. 


•	Key-point based regularization - I am a little confused from the paper - which experiments include the key-points as the "physical regularization" and which include the Canny edges? do some experiments include both?
In Table 3, the third row “Super Point” is a case we use key-point as physical regularization.  
The rest of results in Table 3 use “Canny Edges”. The ablation study in Table 3 is to show that aggregation module is effective and that the physical regularization (or low-level structure regularization) using Canny Edge or key-points are effective. More results are provided here when we use key-point in different network structures (EA, Concat, etc.). 

•	Justification for the "disparity aggregation" block in the paper. Specifically, is there a gradual increase of complexity from option (a) to (e) in Figure 2? For example: "option (a) is the most straightforward way to make a stub-loss, (b)+(c) are non-stub (why?), and the last two are based on concatenation". Specifically, the "jump" from (d) to the selected method (e) is not clear to me. I would advise to elaborate here.
We thank the reviewer for raising this question, which helps us to clarify our motivation and experimental design better. Option (a) is the most-straightforward where we want to that a simple multi-task learning would improve the method. However, as there exists some correlation (a sudden change in disparity may indicate an edge in RGB image but not vice versa ) between “edge” and the disparity map, we want to investigate if we can make use of this observation. The design (b) is to add the “edge” into disparity estimation, this is similar to some earlier methods where edge in RGB are used to guide or smooth the disparity map. This actually lead to artefact or unnatural edge in disparity map, which is also a limitation of EdgeStereo. Option (d) is a case where disparity map to guide edge detection via a simple concatenation. The comparison between (d) and (e) is to show that an aggregation between disparity output and edge features (feature to compute edges) are better than simple concatenation. The option (c) is to make use mutual guide between edge and disparity using a SA gate. From these studies, we want to show quantitatively that using edges to guide the disparity estimation can damage the performance compared with the case to use disparity to guide the edge detection. Our further results suggest that this observation still stands if we change “edges” to key-point. 

•	Failure cases - in what cases is this approach not beneficial? Specifically, why did the method decrease the results in cases like:
o	The 3-noc and 3- all for GwcNet in KITTI 2012
o	The D1-fg for GwcNet in KITTI 2015
Our approach essentially changes the convergence of the model by imposing the regularization term. However, there is no guarantee that the new converged model is always better than previous one without the regularization. Therefore, there exist cases where the new model is similar to previous model if the previous model is not over-fitted or the over-fitting is not serious. This is also a reason that the improvement in GwcNet happen to be smaller and even drop slightly.


 I am curious about the choice of edge information and loss function. Does any other low-level information work for stereo matching?
Do the parameters of edge information affect the performance of stereo matching?
I hope the authors can provide some in-depth analysis of the edge information in the stereo matching. For example, the edge information can correct the boundary of the disparity map? or make the non-edge region more smooth?
What is the generalization ability when testing across datasets?
The choice of different edge information in the regularization term does not affect much the performance improvement. Actually, we compared Canny edge with ideal ground truth edge (computed from the annotation) in Scene Flow dataset and our results show that the performance improvements are comparable. We further applied Sobel edge and obtained similar results. Please noted that when key-points (using SuperPoint algorithms) are used, we also get similar improvement. 

In our implementation, we use cv.Canny() from OpenCV with automatical threshold (auto). We have also test our algorithms using different thresholds from xx to xx with a step of xx. Our results show that the performance does not change much for reasonable thresholds from xx to xxx.
In our methods (option d in Figure xx), we actually don’t use edge to guide the computation of disparity map or smooth it. Instead, we actually use disparity to guide the edge detection. As disparity map is used (via concatenation or via disparity aggregation) to feature maps from the edge (or low level structure) detection branch, we are actually imposing an constraint in disparity maps. It can be expected that if the disparity maps suffer from non-smoothness in a relatively homogenous region, it may causes artefacts in edge detection. By this way, we are regular

We have also tested the cross-dataset generalization. We test our models trained from Sceneflow datasets and tested on ETH3D and Mxxx datasets, the results show that the cross-dataset performances are also improved by the regularization. 


            
            
