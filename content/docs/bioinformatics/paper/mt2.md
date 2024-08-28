---
author: "kaya"
date: 2024-08-27
title: "DeepNetBim: deep learning model for predicting HLA-epitope interactions based on network analysis by harnessing binding and immunogenicity information"
categories: ["논문"]
tags: ["2024"]
---

# DeepNetBim: deep learning model for predicting HLA-epitope interactions based on network analysis by harnessing binding and immunogenicity information

## Abstract

**Background**

Epitope prediction is a useful approach in cancer immunology and immunotherapy. Many computational methods, including machine learning and network analysis, have been developed quickly for such purposes. However, regarding clinical applications, the existing tools are insufficient because few of the predicted binding molecules are immunogenic. Hence, to develop more potent and effective vaccines, it is important to understand binding and immunogenic potential. Here, we observed that the interactive association constituted by human leukocyte antigen (HLA)-peptide pairs can be regarded as a network in which each HLA and peptide is taken as a node. We speculated whether this network could detect the essential interactive propensities embedded in HLA-peptide pairs. Thus, we developed a network-based deep learning method called DeepNetBim by harnessing binding and immunogenic information to predict HLA-peptide interactions.

**Results**

Quantitative class I HLA-peptide binding data and qualitative immunogenic data (including data generated from T cell activation assays, major histocompatibility complex (MHC) binding assays and MHC ligand elution assays) were retrieved from the Immune Epitope Database database. The weighted HLA-peptide binding network and immunogenic network were integrated into a network-based deep learning algorithm constituted by a convolutional neural network and an attention mechanism. The results showed that the integration of network centrality metrics increased the power of both binding and immunogenicity predictions, while the new model significantly outperformed those that did not include network features and those with shuffled networks. Applied on benchmark and independent datasets, DeepNetBim achieved an AUC score of 93.74% in HLA-peptide binding prediction, outperforming 11 state-of-the-art relevant models. Furthermore, the performance enhancement of the combined model, which filtered out negative immunogenic predictions, was confirmed on neoantigen identification by an increase in both positive predictive value (PPV) and the proportion of neoantigen recognition.

**Conclusions**

We developed a network-based deep learning method called DeepNetBim as a pan-specific epitope prediction tool. It extracted the attributes of the network as new features from HLA-peptide binding and immunogenic models. We observed that not only did DeepNetBim binding model outperform other updated methods but the combination of our two models showed better performance. This indicates further applications in clinical practice.

## Background

Accurate identification of peptide presentation to major histocompatibility complex (MHC) molecules is of great importance for exploring the mechanism of immune recognition [1]. The human leukocyte antigen (HLA) gene complex encodes MHC proteins in humans. Two main classes of HLA molecules are important in the immunological context. Class I molecules present epitopes to CD8+ T cells, while class II molecules present epitopes to CD4+ T cells. The verification methods for assessing immunogenicity include both MS-based MHC-I binding peptide detection and immunogenicity verification by specific T-cell response assays [2, 3]. Over the past 30 years, many studies have been devoted to predicting T cell recognition of MHC class I epitopes for immunological reactions. For this purpose, two types of computational methods have been developed: allele-specific and pan-specific methods. The former trains one model for every MHC-I allele [4,5,6,7], while the latter considers both peptides and MHCs and pools all different alleles together to train one general model [8,9,10,11,12]. Most accurate methods retrieve data from The Immune Epitope Database (IEDB) [13], where over 73% of the binding data are 9-mer peptides [12].

Many attempts have been made to improve prediction performance. Recently, an ever-increasing amount of mass spectrometry (MS)-based HLA peptidome data has become available. Therefore, there is growing interest in applying these data to peptide-HLA interaction studies [14]. Some methods also consider proteasomal cleavage and transporter-associated antigen processing (TAP)-mediated peptide transport [15,16,17,18]. In addition, with the rapid development of deep learning methods, shallow and high-order artificial neural networks have been proposed by various research teams, including NetMHC 4.0 [4], HLA-CNN [7], ConvMHC [8], NetMHCPan 4.0 [10], MHCflurry 1.2.0 [19], MHCnuggets [20] and NetMHCstabpan 1.0 [21].

It has been reported that high affinity MHC-epitope interactions tend to be associated with higher immune responsiveness [22]. However, even though MHC binding is necessary for recognition by T cells, it is in itself not sufficient to define immunogenicity. According to a previous report [23], a CD8+ T cell immunogenic peptide is able to form a complex with class I MHC molecules and trigger the activation and proliferation of T cells. Nevertheless, there is a lack of effective strategies available to predict immunogenicity [24]. The existing binding prediction tools are insufficient for neoantigen prediction in clinical applications considering that recognition is also influenced by several other factors, such as the abundance of proteins, immunodominance, antigen processing and the presence of a suitable T-cell repertoire [25,26,27]. Hence, top-ranking candidates predicted by binding affinity for alleles are usually falsely reported as neoantigens [28]. Thus, the demand to understand binding and immunogenic potential should be met eagerly for more potent and peptide-based vaccines.

Here, we observed that the interactive association constituted by HLA-peptide pairs can be regarded as a network in which each HLA and peptide is taken as a node. We believe that in this network, the features of nodes or edges are important and informative for detecting essential HLA-peptide interactive potentials. Thus, in this study, we developed a new network-based deep learning model called DeepNetBim (a deep learning model based on network analysis by harnessing binding and immunogenicity information) for accurate HLA class I pan-specific epitope presentation prediction. To overcome the previously explained deficiencies, we applied the weighted HLA-peptide binding network and immunogenic network into a network-based deep learning algorithm by combining binding and immunogenic models to predict HLA-peptide interactions efficiently.

Extensive tests on benchmark and independent datasets demonstrated that DeepNetBim binding model can significantly outperform other well-known binding prediction tools. Furthermore, the performance enhancement of the combined model, which filtered out negative immunogenic predictions, was confirmed on neoantigen identification by increases in both the positive predictive value (PPV) and the proportion of neoantigen recognition. Overall, DeepNetBim provides a powerful and useful tool for T cell epitope recognition and further immunotherapy practice.

> **요약**
> - DeepNetBim: HLA-펩타이드 상호작용을 예측하는 딥러닝 모델
> - 예측된 많은 결합 분자들이 실제로 면역반응을 유발하지 않는 경우가 많기 때문애 결합력과 면역원성(면역반응을 유발하는 능력)을 잘 이해하고 예측할 수 있는 방법이 필요하다.
> - HLA-펩타이드 쌍 네트워크에서 HLA와 펩타이드는 각각 노드(점)로 취급되며, 이들 간의 상호작용이 네트워크를 형성할수있다.
> - HLA-펩타이드 결합 데이터와 면역원성 데이터를 사용하여 DeepNetBim 모델을 학습시키고 합중 신경망(CNN)**과 어텐션 메커니즘을 통합한 네트워크 기반 딥러닝 알고리즘을 개발.
> - 연구 결과, 네트워크 중심성(네트워크에서의 중요도)을 고려한 모델이 결합력과 면역원성 예측에서 네트워크 특성을 포함하지 않거나 네트워크가 무작위로 섞인 모델에 비해 높은 성능을 보였음.
> - 신항원(neoantigen)은 암세포에서만 발현되는 단백질인데 예측에서도 좋은 성과를 보였음.

## Results

In this study, a new network-based deep learning model called DeepNetBim was proposed (in the following paragraphs, we use ‘HLA’ to refer to ‘HLA class I’ for convenience). The weighted HLA-peptide binding network and immunogenic network were applied to a network-based deep learning algorithm constituted by a convolutional neural network (CNN) and an attention mechanism (Fig. 1). First, the quantitative class I HLA-peptide binding data and qualitative immunogenic data were retrieved from the IEDB database. Then, the weighted HLA-peptide binding network and immunogenic network were constructed separately to acquire quantified individual network features. Next, the encoded peptides and the obtained network features were fed into an attention-based deep learning process. Finally, the predicted binding affinity and binary immunogenic categories of the abovementioned independent models were combined to make the final prediction (“Methods”).

## Methods

**Binding and immunogenic data**

The binding affinity dataset was collected from the IEDB (http://www.iedb.org/) [13]. To generate a pan-specific binding model, we kept only 9-mer peptides, as they collectively represent 73% of the total peptide binding data [12]. The resulting size is suitable for the training step in the following deep learning process. Thus, 104 HLA alleles were retrieved, with each allele having more than 5 peptide binding entries, and 88,913 nonredundant HLA-peptide pairs were obtained after duplicated data were removed.

All peptide binding affinities were measured by half-maximal inhibitory concentration (IC50) values. For this study, the binding affinity was log-transformed to fall in the range between 0 and 1 by using the relation 1− log(x)/log (50,000), where x is the measured binding affinity [37]. Based on strong biological support [38], we used an IC50 threshold of 500 nM (binder ≤ 500 nM) for binary classification. Thus, with this kind of log-transformation, a measured affinity less than 500 nM was assigned an output value above 0.426.

The immunogenic data were retrieved from IEDB. The immune epitope assay data we collected included data from T cell activation assays, MHC binding assays and MHC ligand elution assays. The immunogenic data were generated based on infectious pathogens, diseases, allergens and autoantigens. These binary data were used to create an immunogenic model that provided immunogenic or nonimmunogenic results. A total of 119 HLA alleles were retrieved, with each allele having more than 5 peptide entries. A total of 24,193 nonredundant HLA-peptide pairs were obtained after duplicated data were removed (only considering 9-mer peptides).

**Network construction of binding and immunogenic data**

Original network construction

In the original network construction of DeepNetBim, the interactive association constituted by HLA-peptide pairs could be regarded as a network in which each HLA and peptide was taken as a node. The binding network and the immunogenic network were constructed by applying identical methods separately. In the binding model, the network weight was assigned by the transformed affinity (range from 0 to 1), while in the immunogenic model, it was assigned by the immunogenic binary category (0/1) instead. Additional file 1: Fig. S4 illustrates the network construction process. The weighted HLA-peptide network was established by using the igraph package (version 1.2.4.2) [39] in R (version 3.6.1). However, considering that only positive values are allowed in the igraph package, in the immunogenic model, the weight of the nonimmunogenic peptides with a 0 value was substituted by a small positive value close to 0 (which is 
 in our work, as an approximation method), while the weight of immunogenic peptides was set to 1 [40].

In DeepNetBim, the binding network and the immunogenic network were constructed separately but processed in the same way. Both edge weights and the number of edges were taken into account. For each HLA and peptide, the outcomes of each network were measured by four traditional centrality metrics: degree (the count of the number of other nodes that are adjacent [41]), betweenness (the frequency that a node falls on the shortest paths between pairwise nodes [41]), closeness (the inverse of the sum of the shortest paths from the node to all other nodes [41]) and eigenvector (the eigenvector of the largest eigenvalue of an adjacency matrix that represents the topology [42]). Originally, centrality metrics were used to identify the relative significance of individuals in social network analysis [33]. Here, the introduced metrics could be employed to describe the binding or immunogenicity propensities of the HLA-peptide pairs.

The acquired four centrality metrics for each model were scaled and then assigned to each HLA allele and peptide as new features that were used in the following deep learning model (see Deep learning network architecture part). Thus, for an HLA-peptide pair, eight new features were created (four for HLA and four for peptide).

The transformed network construction

To investigate the effectiveness of network metrics in the DeepNetBim model, we implemented two other transformed types of networks: shuffle networks and random networks for both the binding and immunogenic models. In the shuffle network, the network was reconstructed at the beginning by shuffling the edge weights at different levels (25%, 50%, 75% and 100% shuffle). The 25% shuffle model shuffled a quarter of the network edge weights, while the remaining three quarters of the network structure was unchanged. The 100% shuffle model shuffled all network edge weights. Such transformation only converted the connections of the network, while the overall values of weights were kept the same. Different from the shuffle model, the random model reassigned the network edge weights obeying a [0, 1] uniform distribution. We also acquired the corresponding four types of network metrics for further comparison with the original model. In the results section, we compared the output of the original model with that of the two transformed models to measure the effect of the network metrics.

**Sequence encoding**

Peptide sequence data were encoded in terms of the BLOSUM50 scoring matrix [43], whose encoding scheme was widely used in previous studies [44,45,46]. As BLOSUM50 is a 20 * 20 substitution matrix, each amino acid is represented by its similarity to the other amino acids. Compared with the one-hot encoding strategy (i.e., encoded by 19 zeros and one), the BLOSUM matrix contains prior knowledge about subtle evolutionary and chemical relationships between the 20 amino acids [37, 47, 48]. In our work, peptides that contained amino acids “X”, “B”, and “U” were substituted by the character “Z”, which was not in the amino acid alphabet. Thus, each peptide was encoded to a 9*21 matrix, where each residue was represented by the corresponding row in the BLOSUM matrix. In our framework, the elements of the BLOSUM50 matrix were divided by a scaling factor of 10 to facilitate deep learning model training. In the DeepNetBim model, when predicting a newly added HLA-peptide pair in the network analysis, Euclidean distances between the new peptide and other encoded peptides in the training set were calculated accordingly. After that, the closest peptide network metrics in terms of Euclidean distances were chosen to represent metric features of the newly added peptides, and the median HLA network metrics were chosen to represent metric features of newly added HLA alleles.

**Deep learning network architecture**

The DeepNetBim model passes encoded sequences and metrics features into combined modules of CNN and the attention module to make the final prediction. The attention-based deep neural network was implemented using the Keras library 2.2.4 (https://keras.io). Figure 5 shows the architecture of the deep learning network.

> **method 정리**
> - 결합력 데이터는 **Immune Epitope Database (IEDB)**에서 수집되었습니다. 이 데이터베이스는 다양한 HLA-펩타이드 결합 데이터를 포함하고 있으며, 연구팀은 이 데이터 중에서 9-머 펩타이드만을 사용했습니다. 9-머 펩타이드는 전체 결합 데이터의 73%를 차지하므로, 이를 사용해 판-스페시픽 결합 모델을 구축했습니다. 판-스페시픽 모델은 모든 HLA 유전자를 통합하여 일반적인 모델을 생성하는 방식입니다. 이 과정에서 중복된 데이터를 제거하고 88,913개의 HLA-펩타이드 쌍을 얻었습니다.


