# ooo-Minkun-Model-Collection-V-
Non-parametric Bayesian Model
 - GP
 - DP

## Background Study
### > Gaussian Story
<img src="https://user-images.githubusercontent.com/31917400/73613995-07e46700-45f3-11ea-8760-6ae349c15dd8.png" />


### > Dirichlet Story
### Dirichlet eats `θ` and spits `θ`!
Dirichlet Distribution is a distribution on multinomial distributions. It is a generalized beta distribution. 
<img src="https://user-images.githubusercontent.com/31917400/73676146-c663c280-46ab-11ea-9752-f8a276cb8c20.jpg" />

 - Its **parameter `α`** will be: `n` dimensional vector which is not a pmf, but just a bunch of numbers: `c(α1, α2, α3)`  
   - if α1,α2,α3 are all the same, then the outcome(`θ_i`) appears uniformly.  
   - if α1,α2,α3 are small(<1), the outcome(`θ_i`) appears each corner and edge of the plane
     - Push the distribution to the corners.
   - if α1,α2,α3 are big(>1), the outcome(`θ_i`) appears in the center of the plane
     - Push the distribution to the middle.
   - Thus...α controls the mixture of outcomes. 
     - Turn it down, and we will likely have different values for each possible outcome. 
     - Turn it up, and we will likely have same values for each possible outcome.
 - Its **outcome `θ`** will be: `n` dimensional vector corresponding to some pmf over n possible outcomes: `c(θ_1, θ_2, θ_3) where θ_1 + θ_2 + θ_3 = 1`
It's a distribution over `n` dimensional vectors called "θ". It can be thought of as a multivariate beta distribution for a collection of probabilities (that must sum to 1). 
 - Dirichlet distribution is the conjugate prior for the **multinomial likelihood**.
 - Each `θ_i` has its own `α`...weight(scale) for each distribution of `θ_i`
 - Each `θ_i` has its own distribution...so each is a function???????
 - Total sum of `θ_i` is 1.
<img src="https://user-images.githubusercontent.com/31917400/73609223-77daf900-45c3-11ea-97b6-52158fec1ba0.png" />

### Automatic Hyperparameter Estimation? (determining parameter size??) 
 - [Q] From GMM, how to **get a control over** the latent variable(with multinomial) dynamically? We want to automatically find the **parameter**(proportions) of the latent variable at the end. 
   - The `plate notation` refers to **Random Variables** otherwise parameters. 
 <img src="https://user-images.githubusercontent.com/31917400/73740256-c57c7080-473f-11ea-8bd4-ce698ed37471.jpg" />

   - ### **What we want is `π`... Done and Dusted!
   - Idea 01: `**latent variable parameter** can be treated as a random variable.` 
     - "latent variable" distribution = "multinomial" distribution.
     - Let's make the **parameter** of the latent variable **`"Random Variable"`** by sampling from Dirichlet(α). We can generate or vary the **parameter** for our latent variable distribution, **using Dirichlet(distribution over multinomial)** because Dirichlet is the best way to generate parameters for multinomial distribution. 
     <img src="https://user-images.githubusercontent.com/31917400/73760133-db505c80-4764-11ea-8efa-61a47729f4c7.jpg" />

   - Idea 02: `**latent variable parameter value** can be controlled by data! but how to address **α**? `
     - Multinomial + Dirichlet conjugate relation tells us our parameter value(posterior) can be updated by the introduction of new data(likelihood)!
     - We can get all latent variable parameters `π` with the help of sampling `π` from Dirichlet prior! However, their occurance is not accurate? How to address the hyperparameter α that affects the sampling result ??? 
     <img src="https://user-images.githubusercontent.com/31917400/73765204-1e61fe00-476c-11ea-8bb5-3fbbb7161549.jpg" />

   - Idea 03: `**infinite latent variable parameter values** can be controlled by Random Process that can address **α**`
     - [Note] Random Variable: RV is different from the variable in algebra as RV has whole set of values and it can take any of those randomly. Variable used in algebra cannot have more than a single value at a time: 
       - ex)`random variable_X = {0,1,2,3}`, `variable_K = 1`.
     - [Note] Random(stochastic) Process: Random process is an infinite labeled collection of random variables. Random Process is an event or experiment that has a random outcome, so you can’t predict accurately. In a deterministic process, if we know the initial condition (starting point) of a series of events, we can then predict the next step in the series. Instead, in stochastic processes, although we know the initial condition, we can’t determine with full confidence what are going to be the next steps. That’s because there are so many(or infinite!) different ways the process might evolve. Think of a stochastic process as how smoke particles collide with each other. Their unpredictable movements and collisions are random and are referred to as Brownian Motion. Interest rate is a variable that changes its value over time. It is not straightforward to forecast its movements.
       - ex) Gaussian_P, Drichlet_P, Poisson_P, Brownian motion_P, Markov decision_P,  
     
     - parameter size VS parameter value ???
       - if you know parameter size`t`, then you can expect the parameter value`w` distribution?.    
       - if you know parameter value`w`, then you can expect the data distribution?.
     - Random(Stochastic) Process refers to the infinitely labeled(infinitely hyperparameterized) collection of random variables.
     - With the passage of time(infinite hyper-parameter)`t`?? , the outcomes(resulting parameter)`w`?? of a certain experiment will change...
     <img src="https://user-images.githubusercontent.com/31917400/73839150-32aa0780-480d-11ea-9baa-3e9f4a6c712f.jpg" />

     - ## **But how Random Process can deal with infinite hyper-parameter?
--------------------------------------------------------------------------------------------------------------------

## A. Gaussian Process and Non-linear Problem
For any set `S`, **GP on `S`** refers to a bunch of random variables(pdf functions: `f(x)`) whose index (`x`) is the member of the set `S` such that they can have the following properties: The bunch of variables(pdf functions: `f(x)`) are normally multivariate distributed, thus GP outputs from the mean function and cov function(a.k.a kernel function)!  

It is a distribution over functions. 
<img src="https://user-images.githubusercontent.com/31917400/73618695-60325d80-4621-11ea-8584-e57f3d37c1de.png" />

To make the prediction, take the point (x, f(x)), then try to generate the mean and CI(or cov): Given that having training set, I try to combine the data with the prior of the functions(with mean and cov)..to make my functions smooth. Use a similarity matrix such that our function approximator makes the two points close by when we fit new data to make sure the two hights are also close by.    
<img src="https://user-images.githubusercontent.com/31917400/73652179-2d6a8280-467e-11ea-8651-2c115fe2c1e7.png" />


```
from __future__ import division # from a module "__future__"
import numpy as np
import matplotlib.pyplot as plt

# GaussianProcess' squared exponential
def kernel(a, b) 
   SQdist = np.sum(a**2, 1).reshape(-1, 1) + np.sum(b**2, 1) - 2*np.dot(a, b.T)
   return(np.exp(-0.5 * SQdist)

n = 50
X_text = np.linspace(-5, 5, n).reshape(-1, 1)
K_ = kernel(X_text, X_test)

# Draw samples from the prior 
L = np.linalg.cholesky(K_ + 1e-6*np.eye(n))
f_prior = np.dot(L, np.random.normal(size= (n, 10))) # draw 10 samples(functions)

plt.plot(X_test, f_prior)
```



-------------------------------------------------------------------------------------------------
## B. Dirichlet Process and hyperparameter estimation???
DP is used to determine the `probability of all possible labeling of elements` to categories. Let ![formula](https://render.githubusercontent.com/render/math?math=\theta_1,\theta_2,...\theta_N) be the table(cluster) parameters ![formula](https://render.githubusercontent.com/render/math?math=\phi_k) for each data point. 
 - For example, if we look at a single partition ![formula](https://render.githubusercontent.com/render/math?math=\pi_6) = c({1,3},{2},{6,4,5}), then ![formula](https://render.githubusercontent.com/render/math?math=\theta_1,\theta_2,\theta_3,\theta_4,\theta_5,\theta_6) is {![formula](https://render.githubusercontent.com/render/math?math=\phi_a,\phi_b,\phi_a,\phi_c,\phi_c,\phi_c)}. 

If we incorporate the `CRP(α, N)` and the base distribution `G0` to describe the distribution of `θ`, using the recursion, we can get DP! `N` refers to "total data pt size".
<img src="https://user-images.githubusercontent.com/31917400/74238303-6d101a80-4ccd-11ea-9713-54c2af73542a.jpg" />

So `θ` says ![formula](https://render.githubusercontent.com/render/math?math=\phi_K) which is the parameter value (ex. ![formula](https://render.githubusercontent.com/render/math?math=\mu_K)) that describes the table(cluster). And `θ`(like a mu) follows the distribution that is sampled from the Dirichlet?      

???? 'Z' as a label can be 1,2,3,...∞...and follows Multinomial(`π`). `π` is a parameter vector: c(P(Z=1), P(Z=2), ..P(Z=∞)). The hyperparameter vector `α` controls the **"clumpiness"** of the Dirichlet process. The hyperparameter vector `α` yields a parameter vector `π` which is drawn from `G(A): the distribution of data pt in "A" division` -  Can we go with Gaussian? Sample means follow Gaussian, and we are drawing sample means? 
 - We want to get a control over our latent variable. The latent variable dimensionality is unknown. The latent variable parameter `π`(generated from the Dirichlet Sampling) can be controlled by the **hyperparameter `α`**. But how are you gonna control the **hyperparameter `α`** in Dirichlet?
 - "We assign base probability(pmf `G0` or `H` which is `E[G(?)]`) to each hyperparameter element: (`α1`,`α2`,`α3`...) in Dirichlet"!!!!!!!! Think of the "labels" as a particular random value drawn from the `G(A)`. i.e., all the random variables in a same category share the same value(label), and the values(labels) are distributed according to our chosen base distribution `G(A)`. Now we need to get a control over such **probability assigning mechanism** in Dirichlet. Assuming an infinite number of hyperparameter elements,...an infinite number of multinomial probability values(parameters),...thus, we can think of an infinite number of partitions - A1, A2, A3...- on the event space. We need a mechanism that governs such event space division.  
 - ## key is `Prior` !!!
 - At the end of the day, the hyperparameter control(probability space partitioning to assgin to hyperparameter) can be done by manipulating "prior" (samples from **Dir(`α1*E[G(A1)]`,`α2*E[G(A2)]`,`α3*E[G(A3)]`...)**, then we obtain final posterior for the latent variable parameter `π` by using the updated likelihood (which basically saying how many data pt belongs to which probability partition).
   - Although our initial **hyperparameter `α`** in the prior `Dir(α)` is rubbish,  
     - By building up some `"function"` inside of **hyperparameter `α`**  in `Dir(α)`, 
     - By iterating and updating the prior `Dir(α)`, introducing new datepoints, 
   - we can get a control over **hyperparameter `α`**. 
   - but what `function`?
<img src="https://user-images.githubusercontent.com/31917400/74084847-0095e100-4a6b-11ea-9c01-a3101f6f902a.png" />

 - The output of the Dirichlet (which is a parameter values) can also have a form of "function".
 - Sampling the output "function" from prior: DP(`α`, `H`)
   - Each sample is an instance(parameter `π` for multinomial so..its a probability) but at the same time, a `distribution G(A)`.
   - After that, how to draw a data point "`distribution G(A)`" sampled from the DP(`α`, `H`)? 
   - Actually, we can think of a Non-parametric **prior construction** scheme coz..it's not easy to conceive "A" space that can go to infinity! 
     - Sol 1) Stick-Breaking scheme(sampling distribution)
     - Sol 2) Polya-Urn scheme or Chinese-Restaurant scheme(just sampling point)
     
 - ### [1] Stick-Breaking scheme: 
   - How to deal with pmf on infinite choice? How to get a probability of the pizza with infinite slicing?
   - Sampling a decent distribution 'G(A)', 
     - Using the "adjusted Beta": **GEM(hyperparameter `α`)** which is an adjusted probability value. 
     - times
     - Using the number(count) of events(`π` pt) at the event space `A_k`??? or the count of the event space `A_k`???
     - then Sum them up! It would give you new `π` estimation???? distribution ?????????????????????? Bruna?? 
     - Based on the properties of Beta:
       - Big Hyperparameter: result in big sticks
       - Small Hyperparameter: result in small stick
   <img src="https://user-images.githubusercontent.com/31917400/74085265-33da6f00-4a6f-11ea-9daa-2625a3e15f0b.jpg" />
  
 - ### [2] Chinese-Restaurant-Process scheme:
   - CRP is a sequence of distributions indexed by `K`. Let's say the CRP object is a "partition `π`".
     - partition ![formula](https://render.githubusercontent.com/render/math?math=\pi_n) = c(distr, distr, distr, ..) where n is a total data_size and each subset element(distribution) within a partition is a restaurant table K.    
   - CRP(α, N) is a distribution over all `N`-partitions of the labeled set:     
     - CRP(α, N) = c(![formula](https://render.githubusercontent.com/render/math?math=\pi_n,\pi_n,...\pi_n)) where each ![formula](https://render.githubusercontent.com/render/math?math=\pi_n) refers to a partition. So we have `N`*partition objects. 
   - Given a partition ![formula](https://render.githubusercontent.com/render/math?math=\pi_n), the destination of the next person `n + 1` has the following distribution:  
   <img src="https://user-images.githubusercontent.com/31917400/74105783-cb67bc80-4b58-11ea-9012-20fa99bff2a7.jpg" />
   
   - To think about the mixture model, consider we have **partition objects** as many as the total datasize. From CRP(α, N), we can sample a single partition ![formula](https://render.githubusercontent.com/render/math?math=\pi_N), i.e, we first draw a single partition. 
   - Next, for `each tables` within the drawn partition, we draw a parameter ![formula](https://render.githubusercontent.com/render/math?math=\phi_K) i.i.d. from the base distribution ![formula](https://render.githubusercontent.com/render/math?math=G_0). `This can be prior`.
   - If a data pt ![formula](https://render.githubusercontent.com/render/math?math=x_i) belongs to the table 'K', the data pt will be drawn from some distribution **F(![formula](https://render.githubusercontent.com/render/math?math=\phi_K))** which is parameterized by the corresponding parameter ![formula](https://render.githubusercontent.com/render/math?math=\phi_K). `This can be likelihood`. 
   <img src="https://user-images.githubusercontent.com/31917400/74106820-ba6f7900-4b61-11ea-8025-47155a0d31dc.jpg" />

   - We wish ![formula](https://render.githubusercontent.com/render/math?math=G_0) and F(![formula](https://render.githubusercontent.com/render/math?math=\phi_K)) have a conjugate relation for later. 
   - At first glance, this model does not seem any different from a finite mixture model. However,the difference lies in the behavior as N(# of partitions you have) grows: here, the number of tables(and hence table parameters φ) will grow with N, which does not occur in a finite mixture model. This is the essence of the "non-parametric" aspect.
   - Rich get richer...  popular table..
   - No fixed size of labels with a fixed size of data instances
   <img src="https://user-images.githubusercontent.com/31917400/74086626-7f474a00-4a7c-11ea-86ec-a6514261257b.jpg" />
   
   - Inference:
     - The main goal of clustering is to find the posterior distribution **P(![formula](https://render.githubusercontent.com/render/math?math=\pi_n)|x)** of the cluster assignments! Computing this is intractable due to the sum in the denominator and the growing number of partitions. That's why we use Gibbs Sampling. Let's say..given the previous partition ![formula](https://render.githubusercontent.com/render/math?math=\pi_n), we remove one data pt `x` from the partition (prior) then re-added to the partition (likelihood) to obtain posterior: **P(![formula](https://render.githubusercontent.com/render/math?math=\pi_n)|`x`)**. This gives **new partition** (prior)! 
   - de Finetti's theorem + GibbsSampling
     - The exechangeability is important coz...
       - Chinese-Restaurant-Process is exchangeable process
       - Gibbs Sampling should use the exchangeability coz...its sampling is carried out one label by one label...so can ignore labeling order. 
       <img src="https://user-images.githubusercontent.com/31917400/74086777-309aaf80-4a7e-11ea-8e1d-dd921ab58512.jpg" />

## C. Dirichlet Process Mixture Model   
Think about the parameter of a mixture model. 
 - One hurdle we encounter here is **"sampling from `G`"**, which has countably many atoms(sticks). There is also an exact approach that generates atoms "on the fly" as needed, and exploits the fact that only finitely many atoms are needed in practice.  
 <img src="https://user-images.githubusercontent.com/31917400/74238858-8bc2e100-4cce-11ea-9d4a-028ad20f205e.jpg" />

 - For the sampling algorithm, it is convenient to include table assignment variable `Z` to indicate which table our data pt ![formula](https://render.githubusercontent.com/render/math?math=\x_i) belongs to.  
 <img src="https://user-images.githubusercontent.com/31917400/74239298-7ac69f80-4ccf-11ea-9474-e0eb493dee18.jpg" />









### > Implementation
 - Step I. Initial Labeling(assign table) to every point.
 - Step II. While Gibbs Sampling Iteration with each data instance:
   - For each data instance:
     - Remove the instance from the label
     - Calculate the prior labal 
     - Calculate the likelihood 
     - Calculate the posterior
     - Sample the label from the posterior
     - Update the component parameter


































