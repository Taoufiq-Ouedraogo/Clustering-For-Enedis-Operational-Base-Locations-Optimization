# Optimizing Enedis Operational Base Locations in Brittany (Rennes Data Challenge 2023)

------------------

<br>


*Enedis is a company that operates and maintains most of the electricity distribution network in France*
- *it does not produce electricity and does not sell it*
- *it focuses only on distribution and reliability of the electrical grid*

<a href="https://www.enedis.fr" target="_blank"><img src="https://upload.wikimedia.org/wikipedia/fr/archive/7/77/20220206174115%21Logo_enedis_header.png" width="400"></a> &nbsp;&nbsp; 


<br>


### Presentation

-------------


The project focuses on optimizing the location of operational bases (OB) for Enedis in the Brittany region.  

The goal is to reduce technician response times by better placing operational bases and assigning communes to them efficiently. This will:
- reduce power outage duration
- improve service quality for customers
- increase team efficiency



<br>



### Objective

--------------


We will optimize location of operational bases and assign each commune to a base in order to reduce intervention times:
1. each commune must be assigned to exactly 1 operational base
2. travel time between commune and base should not exceed 30 minutes
3. communes have different weights based on intervention activity levels





<br>


### Approach

We compare several clustering strategies to model how operational bases should be placed across Brittany:
- partitioning communes into $k$ groups
- minimizing within-cluster spatial dispersion and improving operational relevance



|   Approach    |   Model  | Objective Function Minimized  |
|-----|-----------|------|
|  **Baseline geographic clustering**   |    Reference model using only spatial coordinates <br><br>  Each commune is represented by: $$x_i = (x_i^{lon}, x_i^{lat})$$  |  Standard objective: <br> $$J = \sum_{k=1}^{K} \sum_{i \in C_k} \lVert x_i - \mu_k \rVert^2$$   <br><br>  -  $C_k$: cluster $k$ <br> - $\mu_k$: centroid of cluster $k$   |     
|  **Activity-weighted clustering**    |  Introduces operational demand through weights  <br><br>  Each commune receives a weight: <br> $$w_i = \text{activityLevel}_i$$  | Weighted objective: <br> $$J = \sum_{k=1}^{K} \sum_{i \in C_k} w_i \, \lVert x_i - \mu_k \rVert^2$$  <br><br>  $$\mu_k = \frac{\sum_{i \in C_k} w_i x_i}{\sum_{i \in C_k} w_i}$$  |
| **Activity + accessibility weighting**  |    Introduces travel difficulty between communes  <br><br> Weight: <br>  $$w_i = \frac{\text{activityLevel}_i}{\text{minutes}_i + \varepsilon}$$    <br><br> -  $\text{minutes}_i$: travel time from commune $i$  <br> - $\varepsilon$ avoids division by 0   |     $$J = \sum_{k=1}^{K} \sum_{i \in C_k} w_i \, \lVert x_i - \mu_k \rVert^2$$  |
|     **Activity + average accessibility weighting**   |   Uses average accessibility per commune <br><br>   Average travel time:  <br> $$\bar{t}_i = \frac{1}{\mid N_i \mid} \sum_{j \in N_i} t_{ij}$$   <br><br>   Weight:  <br> $$w_i = \frac{\text{activityLevel}_i}{\bar{t}_i + \varepsilon}$$    |       $$J = \sum_{k=1}^{K} \sum_{i \in C_k} w_i \, \lVert x_i - \mu_k \rVert^2$$  |

 


 
<br><br>

* Activity-weighted clustering (best): best overall trade-off between demand alignment, spatial coherence, and operational efficiency, while keeping clusters stable and well balanced
* Geographic clustering (strong baseline): very stable and efficient spatial structure, but ignores demand, limiting operational relevance
* Per-trip accessibility + demand weighting (mixed): slightly improves targeting of accessible high-demand areas, but introduces fragmentation and mild efficiency loss
* Average accessibility-adjusted demand weighting (worst): highly unstable clustering with strong imbalance in size, workload, and distance, leading to poor and inconsistent operational performance


<div style="display: flex; justify-content: space-between; gap: 15px;">
    <img src="data/img/spatial_clustering.png" style="width: 100%;">
</div>


<div style="display: flex; justify-content: space-between; gap: 15px;">
    <img src="data/img/activity_based_clustering.png" style="width: 100%;">
</div>


<div style="display: flex; justify-content: space-between; gap: 15px;">
    <img src="data/img/activity_access_based_clustering.png" style="width: 100%;">
</div>

<div style="display: flex; justify-content: space-between; gap: 15px;">
    <img src="data/img/activity_avg_access_based_clustering.png" style="width: 100%;">
</div>





<br>


<div style="display: flex; justify-content: space-between; gap: 15px;">
  <div style="width: 50%;">
    <img src="data/img/plot_activity_level_count_by_clustering.png" style="width: 100%;">
  </div>
  <div style="width: 50%;">
    <img src="data/img/plot_dist_to_centroid_by_clustering.png" style="width: 100%;">
  </div>
</div>
