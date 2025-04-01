# Q1: 

The paper does not live up to its claims. Despite modest claims of addressing oversmoothing (which can be addressed by many approaches nowadays), the authors do not show results with different number of layers (very deep models).

# A1: 

Thank you for your valuable feedback.

We would like to clarify a potential misunderstanding. Our method does not address oversmoothing by increasing the number of GNN layers. Instead, **CGDConv mitigates oversmoothing from the perspective of diffusion control**, by adaptively limiting the number of message-passing steps through a theoretically grounded convergence criterion $K_0$ and constraint factor $\beta = 2K_0$ (Section 3.1, Eq. 7–9). This design avoids excessive feature propagation and homogenization — the essence of oversmoothing — without requiring deep model architectures.

As you suggested, we have conducted relevant experiments under **different diffusion depths**, which play an analogous role to model depth in traditional GNNs. These results are reported in **Table 4**, where we vary $\beta$ and show the corresponding performance across multiple datasets and backbones. We believe these results address your concern by directly evaluating the impact of propagation depth and demonstrating the robustness of our diffusion strategy.

**CGDConv is designed as a plug-in diffusion strategy that is orthogonal to GNN layer depth** — it can complement deep models, but does not depend on depth to function effectively. Indeed, even with a standard 2-layer GCN backbone, CGDConv outperforms deeper or more complex diffusion-based baselines such as GDC and ADC (see Table 2 and ablation studies in Table 5), which further demonstrates the method's effectiveness.



# Q2: 

The evaluation that was done in the paper is ok, but it is not sufficient. This is because the datasets chosen here are known to suffer from different issues, as shown in [1,2]. Thus, the authors should show the performance of their method also on other datasets like OGB [3] and new heterophilic datasets [2]. It will also be more convincing if the authors show results on additional tasks, not only node classification. Additionally, the authors do not compare their method with recent state of the art methods, which conveys a misleading portrait of the current state of the art. I kindly ask the authors to revise their tables to reflect up-to-date architectures.

```
[1] Pitfalls of Graph Neural Network Evaluation
[2] A critical look at the evaluation of GNNs under heterophily: Are we really making progress?
[3] Open Graph Benchmark: Datasets for Machine Learning on Graphs
```

# A2:

Thank you for your valuable suggestions regarding evaluation breadth and dataset diversity.

We fully agree that evaluating on larger and more challenging datasets like OGB is important. In response, we attempted to run CGDConv on **OGB-arxiv**, and encountered a **critical scalability bottleneck** due to the size of the diffusion matrix. Specifically, computing the PPR-based diffusion matrix involves inverting a $169{,}343 \times 169{,}343$ adjacency matrix, which requires approximately **118GB of memory**. This leads to a **silent failure** on standard hardware: the process terminates without error output, as shown in the attached runtime log (see **Figure Re.1**).


This issue is not specific to our method — it arises from the matrix inversion operation commonly used in diffusion-based models like GDC and ADC, which are also not scalable to such graph sizes without approximation. We acknowledge that handling large-scale graphs remains a crucial open challenge and plan to investigate efficient and scalable solutions (e.g., sparsified diffusion, Monte Carlo estimation) in future work.

Regarding the **dataset choice and heterophily**, we emphasize that our method was designed to improve message passing in both homogeneous and heterogeneous settings. In particular, we include seven widely used heterophilic datasets (e.g., Squirrel, Chameleon, Cornell, etc.) and demonstrate consistent gains over strong baselines like GloGNN and SNGNN. We believe this provides meaningful insight into the generality of our method, even if not exhaustive.

We also appreciate the suggestion to explore **additional tasks beyond node classification**, and plan to extend CGDConv to link prediction and graph-level tasks in future work. Due to space constraints, we focused this work on node classification to establish the effectiveness of our core idea.

Lastly, we would like to clarify that this work was conducted in 2024, and we have already included baselines up to 2023, such as GloGNN and SNGNN. As of our submission, few relevant diffusion-based methods had been published in 2024. We sincerely appreciate the suggestion and are willing to incorporate additional recent 2024 baselines in the final version to further strengthen our comparisons.

<div align="center"><strong>Figure Re.1: Silent crash when running the OGB dataset. When the Python process exceeds the memory limit, the operating system will directly terminate the process without throwing a Python exception. The "adj matrix over" you see is the last successfully executed step, after which the process is killed by the system.

<img width="650" alt="image" src="https://github.com/user-attachments/assets/dc034a0d-b8c9-48e7-96f1-28435491597a" /></strong></div>

# Q3: 

The paper is very much related to previous works, however it lacks citations and comparison with methods that are very much relevant. I provide examples below.

# A3:

We thank the reviewer for raising this important point.

We would like to clarify that we have dedicated **Section 2 (Preliminary)** to providing theoretical background and introducing the foundations of graph diffusion. In particular, **Section 2.2 explicitly discusses standard graph diffusion processes**, including the formulation of the diffusion matrix based on Personalized PageRank (Eq. 2), along with appropriate citations [e.g., Gasteiger et al., 2019].

In **Section 3.1**, the formulation of Constrained Graph Diffusion (CGD) is our proposed extension based on these foundations. To avoid confusion, we explicitly indicated this with the sentence *“The diffusion process can be expressed as...”*, referring to the previous equation and building upon it. Nevertheless, we understand the reviewer’s concern that this connection may not have been sufficiently emphasized or the references may not have been repeated explicitly in that section.

We understand that this link may not be sufficiently explicit for readers unfamiliar with the structure. We appreciate your suggestion and will revise the paper to make the connection clearer and better reflect the relationship to prior work. For example, we will include a clarifying sentence such as:

> *"Based on the standard PPR-based diffusion formulation in Eq. (2) [Gasteiger et al., 2019], we introduce a constrained variant to avoid excessive message propagation and computational redundancy."*

We will also reinforce citations in later sections and ensure that the unique contributions of our model are clearly distinguished from the established foundations.

# Q4: 

The experiments provided are OK, but not sufficient to actually show why the proposed method is better, as previously discussed.

# A4:

Thank you again for your feedback.

We understand your concern that the current experimental setup may not fully showcase the superiority of our proposed method. We would like to respectfully clarify that our experimental section includes:

- **Nine diverse benchmark datasets**, covering both **homogeneous** and **heterogeneous** graphs (including low-degree and heterophilic structures);
- **Comprehensive comparisons** with **nine baseline models**, including strong diffusion-based and heterophily-aware GNNs (e.g., GDC, ADC, SNGNN, GloGNN);
- A detailed **ablation study** (Table 5) that isolates the contributions of our two core components — constrained diffusion (CD) and feature information flow routing (FIFR);
- A **parameter sensitivity analysis** for both the constraint factor $\beta$ and balance factor $\alpha$ (Section 5.3), showing how CGDConv adapts to graph structure;
- A focused **evaluation on low-degree nodes** (Appendix Table A.2), demonstrating how CGDConv improves classification for weakly connected or noisy nodes — a known challenge in graph learning.

These results consistently show that CGDConv achieves superior or competitive performance in a variety of settings. Importantly, our method is **model-agnostic and plug-and-play**, making it broadly applicable as a graph augmentation technique.

We believe that the **combination of effectiveness, interpretability, and generality** is what sets CGDConv apart. Nonetheless, we appreciate your suggestion and are actively working to extend our evaluation to other tasks (e.g., link prediction) and larger datasets, as noted in our responses above.



# Q5: 

The paper is very much related to previous works, however it lacks citations and comparison with methods that are very much relevant. I provide examples below.Essential References Not Discussed: The authors should cite, discuss, and compare where possible with the following works, which are directly related to this work: 

```
[4] Feature Transportation Improves Graph Neural Networks (AAAI 2024). Here it was proposed to learn the diffusion process and allow directed information flow with advection, and it belongs to the graph neuro ODE family of architectures.

[5] Graph Neural Convection-Diffusion with Heterophily (IJCAI 2023). Similarly, to [4] it was proposed to learn a convective term that allows to direct the information flow, and is also a graph neuro ODE architecture.

[6] Edge Directionality Improves Learning on Heterophilic Graphs. This paper suggest learning directional information flow.

Missing references on the background of diffusion (differential equation) based GNNs:

[7] Graph Neural Ordinary Differential Equations

[8] GRAND: Graph Neural Diffusion

[9] PDE-GCN: Novel Architectures for Graph Neural Networks Motivated by Partial Differential Equations
```

# A5:

We thank the reviewer for pointing out these additional related works and agree that some of them are highly relevant to our problem setting. We address them below:

- For references [4], [5], and [6], we acknowledge that these works also explore **directional message passing**, often in the context of **graph neuro ODEs** or with **advection-like mechanisms**. While our approach is not grounded in differential equations, we share the common motivation of **directing feature flow** to mitigate oversmoothing and over-diffusion. We will incorporate discussion of these works into the revised version, emphasizing the distinction that our method remains discrete, plug-and-play, and lightweight (i.e., not requiring ODE solvers or continuous-time dynamics).

- For references [8] GRAND and [9] PDE-GCN, we agree that they are theoretically relevant — both propose diffusion-based or PDE-inspired models. However, as shown in the screenshots below, **neither has released code**, which makes **direct comparison infeasible** under our current reproducible evaluation setup (<u>See Figure Re.2 and Figure Re.3</u>). We followed the experimental protocol from Gasteiger et al. (2019) for fair baselines and comparison, as described in Appendix B. Without official implementations or compatible splits, we are unable to ensure fairness in comparison (<u>See Figure Re.4</u>). 

- Regarding [7] Graph Neural ODEs, we acknowledge this important background and will add appropriate citation and discussion in Section 2 (Preliminary), clarifying that our approach focuses on discrete-step constrained diffusion rather than continuous-time ODE modeling.

We appreciate the reviewer’s comprehensive feedback and will ensure that all these works are acknowledged and appropriately positioned in the revised version. Our goal is not to replace but rather to **complement** existing approaches with a **general-purpose, architecture-agnostic diffusion enhancement**.

<div align="center"><strong>Figure Re.2: Screenshot from the official GitHub link provided in the [8] GRAND. This showing a *404 error*, indicating that the codebase is not publicly accessible at the time of our submission. This makes direct, reproducible comparison with GRAND infeasible.

<img width="750" alt="image" src="https://github.com/user-attachments/assets/5f9dfb21-2663-40b6-b501-e271e8b7218d" /></strong></div>

<div align="center"><strong>Figure Re.3: Excerpt from the [9] PDE-GCN. Its supplementary material, where **no valid links or code archives** could be found. As shown in the figure, despite mentioning code availability, no actual code repository was provided publicly.

<img width="650" alt="image" src="https://github.com/user-attachments/assets/6c5fa416-28d1-41b4-ba0a-476c576a1299" /></strong></div>

<div align="center"><strong>Figure Re.4: Statement of the fairness setting (following industry standards) for the experiments that we have conducted in Appendix B.

<img width="650" alt="image" src="https://github.com/user-attachments/assets/5ed0c454-ddc8-4d42-8847-60123eb9d068" /></strong></div>

# Q6: 

There are claims in the abstract which are not true, like 'was proposed to address this issue, which expands the depth of message passing by leveraging generalized graph diffusion to capture global structural relationships' and 'However, existing models based on GDC lack effective control over the strength and direction of the diffusion process, leading to overdiffusion and unnecessary feature homogenization of nodes.'.

```markdown
- The figures do not print well on adobe acrobat.
- There are claims throughout the paper not backed up by proofs / references.
- There is no discussion of the complexity of the method or report of the runtimes of the method.
- The authors use wrong terminology. The graph are homophilic/heterohopilic, not homogenous/heterogenous
```

# A6:

We thank the reviewer for this detailed and multifaceted feedback. We address the concerns point-by-point below:

1. **Clarification of Abstract Claims**  
   We respectfully clarify that our statement in the abstract—"GDC... expands the depth of message passing by leveraging generalized graph diffusion to capture global structural relationships"—is based on the original formulation of GDC [Gasteiger et al., 2019], which introduces a diffusion matrix using personalized PageRank to extend node-level interactions beyond one-hop neighborhoods. This approach has been widely interpreted as a global diffusion mechanism in follow-up literature.  
   Similarly, our statement that "existing models based on GDC lack effective control..." is based on empirical observations of GDC's **fixed diffusion depth** and **undirected diffusion nature** (i.e., symmetric propagation), which can indeed lead to oversmoothing, especially in heterophilic graphs. We have made these assumptions explicit in Sec. 3.1 and supported them through controlled experiments and ablation studies (e.g., Table 4).

2. **Figures Not Printing Well in Adobe Acrobat**  
   All figures were exported as **PDF vector graphics** to ensure scalability and clarity at high resolution. We are uncertain why Acrobat displayed them poorly, and it may be due to rendering glitches on specific systems. Nevertheless, we will optimize figure layout (e.g., splitting large multi-plot figures into subfigures) in the camera-ready version to ensure readability and better print rendering.

3. **Claims Not Backed by Proofs or References**  
   We believe most technical claims are backed by references in Section 2 (Preliminary) or by derivations in Section 3 (Method). For example:
   - Eq. (2)–(5) follow standard formulations from GDC/PPR.
   - The “Information Reduction Assumption” is introduced in Appendix A with theoretical motivation and experimental support (Table 6).
   - Runtime and over-diffusion control are analyzed in Section 4.3 with experiments across multiple values of the constraint factor β.  
     We appreciate the suggestion and will carefully re-review the manuscript to ensure every non-obvious claim is either proved or explicitly cited.

4. **No Runtime or Complexity Analysis**  
   Thank you for pointing this out. In our revision, we will include both theoretical complexity analysis of our method (See Figure Re.5).

5. **Terminology: Homophilic vs. Heterophilic (not Homogeneous/Heterogeneous)**  
   We appreciate the correction. While the term "homogeneous graph" is used to refer to graphs without typed nodes or edges, we agree that **"homophilic" and "heterophilic"** more accurately reflect the **semantic relationship between node features and connections**. We will revise the terminology throughout the paper to reflect this distinction.

<div align="center"><strong>Figure Re.5: Additional analysis on complexity

<img width="650" alt="image" src="https://github.com/user-attachments/assets/1b528e56-a1b1-4bd6-a78d-851716e92a27" /></strong></div>

# Q7: 

What is the main benefit of the method? Can you show how it solves inherent problems with GNNs such as oversquashing or oversmoothing? How do you distinguish it from existing methods ?

# A7: 

We appreciate your question regarding the core benefits of our method and how it addresses fundamental issues in GNNs.

1. **Oversmoothing Mitigation**
   Our method explicitly constrains the graph diffusion process by **limiting the number of diffusion steps** via a structure-aware criterion (Eq. (6)–(9)), which prevents feature homogenization across distant nodes. This design mitigates oversmoothing without requiring deep models or architectural changes. As shown in Table 6, we reduce diffusion steps by an average of 59.77%, while still improving classification accuracy on heterophilic benchmarks. We further verify this in ablation studies (Table 5) and low-degree node performance (Appendix Table 10).

2. **Oversquashing Alleviation**
   Oversquashing arises when too much information is compressed into fixed-size node representations through narrow message-passing paths. Our **Feature Information Flow Routing (FIFR)** introduces a non-structural guidance mechanism that **redirects information along semantically similar node pairs**, creating alternative (non-local) paths that improve representation diversity. This effectively alleviates squashing along sparse or weakly connected structures, as demonstrated by performance gains on low-degree nodes and heterophilic graphs (Table 7, Appendix A).

3. **Distinction from Existing Methods**
   Unlike prior approaches that increase model depth (e.g., residual GNNs) or inject attention (e.g., GAT), CGDConv works as a **lightweight preprocessing module** that is model-agnostic and can be seamlessly plugged into any GCN. Compared to GDC and ADC, CGDConv is distinguished by:
   - Dynamic, graph-specific diffusion step estimation (not fixed depth),
   - Integration of non-topological feature routing for directional control,
   - Compatibility with both homophilic and heterophilic graphs (Fig. 4 and Table 3).
