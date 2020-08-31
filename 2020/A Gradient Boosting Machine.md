# A Gradient Boosting Machine: Tree Boost

[![N|Solid](https://cldup.com/dTxpPi9lDf.thumb.png)](https://nodesource.com/products/nsolid)

[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

Boosting algorithms have enjoyed a wilde popularity in academia and industry, when function estimation/approximation is in need. This article will introduce a gradient boosting machine that has high performance in reality.

## An Old Problem: Function Estimation

In the function estimation or "predictive learning", one uses a "traning" sample $$\left\lbrace y_{i}, x_i \right\rbrace_1^N$$, to obtain an estimate or approximation $$\hat{F}(x)$$, of the function $$F^*(x)$$ mapping $$x$$ to $$y$$, that minimizes the expectation of some specified loss function $$L(y, F(x))$$ over the joint distribtion of all $$(y,x)$$-values,

$$F^* = arg~min_F~\mathbb{E}_{y,x}~L(y,F(x)) = arg~min_F~\mathbb{E}_x[\mathbb{E}_y(L(y, F(x)))|~x].$$

### Two Approaches in Function Estimation

A common procedure procedure is to restrict $$F(x)$$ to be a member of a parameterized class of functions $$F(x;P)$$, where $$P = \left\lbrace P_1, P_2\cdots \right\rbrace$$ is a finite set of parameters whose joint values identify individual class members. In this article, we focus on "additive" expansions of the form,
$$\begin{aligned}
F(x;\left\lbrace \beta_m, \alpha_m \right\rbrace_1^M) = \sum_{m=1}^M \beta_m h(x;\alpha_m)
\end{aligned}.$$ 
In general, choosing a parameterized model $$F(x;P)$$ changes the function optimization problem to one of **parameter optimization in parameter space**. Such optimization proceedures could result in infeasible solutions or cost expensive computational power. In such cases, rather than considering function estimation in the perspective of parameter space, we take a "non-parametric" approach and apply **numerical optimization in function space**. That is, we consider $$F(x)$$ evaluated at each point $$x$$ to be a "parameter" and optimize over the functional space. In function space there are an infinite number of such "parameters", but in data sets only a finite number $$\left\lbrace F(x_i) \right\rbrace_1^N$$ are involved. 

We stick to the optimization in parameter space and make a connection between stagewise additive expansions and steepest-descent minimization.

## Gradient Boost

The general gradient descent boosting uses a "greedy stegewise" approach, where for $$m = 1,2,\cdots, M,$$
$$(\beta_m, \alpha_m) = arg~min_{\beta, \alpha}~\sum_{i=1}^N~L(y_i, F_{m-1}(x_i) + \beta h(x_i;\alpha)),$$
and then 
$$F_m(x) = F_{m-1}(x) + \beta_mh(x;\alpha_m).$$

This form has been widely used in different areas. In signal processing this stagewise strategy is called "matching pursuit" where the loss function $$L(y,F)$$ is squared-error loss and the $$h(x;\alpha)$$ are called basis functions. In machine learning, the procedure of developing the additive components after numerical optimizing, is called "boosting". Common names in machine learning for $$h$$ are "weak learner" or "base learner", especially when $$h$$ is a classification tree.

The procedure above has a clear form but the solution to $$(\beta,\alpha)$$ could be difficult to obtain for a particular loss $$L(y, F)$$ and/or base learner $$h(x;\alpha)$$. So on second look over the procedure, we find that given any approximator $$F_{m-1}(x)$$, the additive component $$\beta_mh(x;\alpha_m)$$ can be viewed as the best greedy step toward the data-based estimate of $$F^*(x)$$, with constraint that the step "direction" $h(x;\alpha_m)$ belongs to the assumpted parameterized/functional class. It can thus be regarded as a steepest descent step under some constraint. Roughly speaking, the constraint is applied to the unconstrianed solution by fitting $$h(x;a)$$ to the "pseudoresponses" $$\left\lbrace \tilde{y}_i = -g_m(x_i) \right\rbrace_1^N$$, with $$-g_m(x_i)$$ being the unconstrained negative gradient,

$$\begin{aligned}
-g_m(x_i) = \left(\frac{\partial L(y_i,F(x_i))}{\partial F(x_i)} \right)_{F = F_{m-1}}
\end{aligned}.$$

This replaces the difficult function minization problem by least-squares function minimization, followed by only a single parameter optimization. Thus, one can use this approach to minimize any differentiable loss $L(y,F)$ in conjunction with forward stagewise additive modeling.

In summary, it leads to the generic gradient boosting algorithm suing steepest-descent.
>***Algorithm 1: Gradient Boosting***
> 1. $$F_0(x) = arg min_{\rho}\sum_{i=1}^N L(y_i, \rho)$$
> 2. For $$m = 1$$ to $$M$$ do:
> $$\begin{aligned}
\tilde{y}_i = \left(\frac{\partial L(y_i,F(x_i))}{\partial F(x_i)} \right)_{F = F_{m-1}}
\end{aligned},~i = 1,\cdots, N$$
> $$\alpha_m = argmin_{\alpha,\beta}\sum_{i=1}^N \left[\tilde{y}_i - \beta h(x_i;\alpha) \right]^2$$
>$$\rho_m = argmin_{\rho}\sum_{i=1}^N L(y_i, F_{m-1} + \rho h(x_i;\alpha_m))$$
>$$F_m(x) = F_{m-1}(x) + \rho_m h(x; \alpha_m)$$
> end for

For regression problems, $$y\in \mathbb{R}$$, a natural choice for estimating the smoothed negative gradient would be least-squares. In classification problems where $$y\in \left\lbrace -1,1 \right\rbrace$$, the quantity $$yF$$ is called the "margin" and the steepest-descent is performed in the space of margin values, rather than the space of function values $$F$$. A different strategy is popular of casting regression into the framework of classification in the context of the AdaBoost algorithm.

Basically, we could choose different loss criteria and apply the gradient boosting strayegy, and develop different algorithms.
-  LS Gradient Boosting
The most popular loss crteria is the least-squares(LS), where $$L(y,F) = (y-F)^2/2$$. The pseudo-response will be $$\tilde{y}_i = y_i - F_{m-1}(x_i)$$. Thus, the gradient boosting on squared-error loss produces the usual stagewise approach of iteratively fitting the current residuals.

- LAD Gradient Boosting
In this case, the locss criteria is least absolute deviation(LAD) with $$L(y, F) = |y-F|$$. The pseudo-response will be the sign of current residuals. 

- Regression Tree-based Gradient Boosting
Here we consider the special cse where each base learner is a regression tree. Each regression tree model itself has the additive form 
$$\begin{aligned}
h(x;\left\lbrace b_j, R_j\right\rbrace_1^{J}) = \sum_{j=1}^J b_j \mathbb{1}(x\in R_j).
\end{aligned}$$
Here $$\left\lbrace R_j \right\rbrace_1^J$$ are disjoint regions that collectively cover the space of all joint values of the predictor variables $$x$$. The regions are represented by the terminal nodes of the correpsonding tree. Because the regions are disjoint, the formula above is equivalent to the prediction rule: if $$x\in R_j$$ then $$h(x) = b_j$$. 

    For a regression tree, the update at line 6 of ***Algorithm 1*** becomes
    $$\begin{aligned}
    F_m(x) = F_{m-1}(x) + \rho_m \sum_{j=1}^J b_{jm} \mathbb{1}(x\in R_{jm}).
    \end{aligned}$$
    The $$\left\lbrace  R_{jm} \right\rbrace_1^J$$ are the regions defined by the terminal nodes of the tree at the $$m$$th iteration. They are constructed to predict the pseudo-responses $$\left\lbrace \tilde{y}_i\right\rbrace_1^N$$ by least-squares. The $$\left\lbrace b_{jm}\right\rbrace$$ are the corresponding least-squares coefficients, given by $$b_{jm} = \text{ave}_{x_i \in R_{jm}}\tilde{y}_i$$. By line search, we get the scaling factor $$\rho_m$$ and the update can be alternatively expressed as 
    $$\begin{aligned}
     F_m(x) &= F_{m-1}(x) +  \sum_{j=1}^J \rho_mb_{jm} \mathbb{1}(x\in R_{jm})\\
            &= F_{m-1}(x) +  \sum_{j=1}^J \gamma_{jm} \mathbb{1}(x\in R_{jm}).
    \end{aligned}$$
    We can view the last expression as adding $$J$$ separate bases functions at each step $$\left\lbrace \mathbb{1}(x \in R_{jm})\right\rbrace_1^J$$. 
    
    We can further improve the quality of the fit by using the optimal coefficients for each of these separate basis functions, and these optimal coefficients are the solution to
    $$\left\lbrace \gamma_{jm}\right\rbrace_1^J = argmin_{\left\lbrace \gamma_{j}\right\rbrace_1^J} \sum_1^N L(y_i, F_{m-1}(x_i) + \sum_1^J \gamma_j \mathbb{1}(x\in R_{jm}))$$.
    
    Owing to the disjoint nature of the regions produced by regression trees, this reduces to $$\gamma_{jm} = argmin_{\gamma} \sum_{x_i\in R_{jm}} L(y_i, F_{m-1}(x_i) + \gamma )$$.
    
    The solution above recovers the least square regression gradient boosting with regression tree basis. For the case of LAD regression the solution becomes  $$\gamma_{jm} = \text{median}_{x_i\in R_{jm}}\left\lbrace y_i-F_{m-1}(x_i) \right\rbrace,$$ which is simply the median of the current residuals in the $$j$$th terminal node at the $$m$$th iteration. At each iteration a regession tree is built to best predict the *sign* of the current residuals $$y_i-F_{m-1}(x_i)$$, based on a least-squares criterion. Then the approximation is updated by adding the median of the residuals in each of the derived terminal nodes. We summarize this algorithm as follows,
    >***Algorithm 2: LAD Tree Boost***
    > 1. $$F_0(x) = \text{median}\left\lbrace y_i \right\rbrace_1^N$$
    > 2. For $$m = 1$$ to $$M$$ do:
    > $$\begin{aligned}
    \tilde{y}_i = \text{sign} (y_i - F_{m-1}(x_i))
    \end{aligned},~i = 1,\cdots, N$$
    > $$\left\lbrace R_{jm} \right\rbrace_1^J = J\text{-terminal node tree}(\left\lbrace \tilde{y}_i, x_i \right\rbrace_1^N)$$
    >$$\begin{aligned}
    \gamma_{jm} = \text{median}_{x_i \in R_{jm}} (y_i - F_{m-1}(x_i))
    \end{aligned},~j = 1,\cdots, J$$
    >$$F_m(x) = F_{m-1}(x) + \sum_{j=1}^J \gamma_{jm} \mathbb{1}(x \in R_{jm})$$
    > end for
    
    ***Algorithm 2*** is highly robust. The trees use only order information on the individual input variables $$x_k$$, and the pseudoresponses $$\tilde{y}_i\in \left\lbrace -1, 1 \right\rbrace$$. The terminal node updates are based on medians. It is fast since it uses least-squares to induce the trees. Squared-error loss is much more rapidly.

- M/Huber Gradient Boosting
    M-regresion techniques attempt resistance to long-tailed error distributions and outliers while maintaining high efficiency for normally distributed errors. We consider the Huber loss function
    $$\begin{aligned}L(y,F) = 
    \begin{cases}
    \frac{1}{2}(y-F)^2, & |y-F| \leq \delta, \\
    \delta(|y-F| - \delta/2), & |y-F| > \delta.
    \end{cases}\end{aligned}$$

    The value of the transition point $$\delta$$ defines those residual values that are considered to be "outliers", subject to absolute rather than squared-error loss. According to the motivations underlying robust regression, the boosting regression trees based on Huber loss should have properties similar to that of least-squares boosting for normally distributed errors, and similar to that of least absolute deviation regression with very long-tailed distributions. For error distributions with only moderately long tails it can have performance superior to both.


- L/Logistic Binomial Log-likelihood Gradient Boosting
    When the response is binary, we develop a gradient-descent boosting algorithm for the binary classification, where the loss function is negative binomial log-likelihood, $$L(y, F) = log(1 + exp(-2yF)), ~~y\in \left\lbrace -1, 1 \right\rbrace,$$ with $$F(x) = \frac{1}{2}~log\left[\frac{\mathbb{P}(y=~~1|x)}{\mathbb{P}(y=-1|x)}\right]$$. Following similar procedure with the general gradient boosting in ***Algorithm 1***, we have a two-class logistic regression and classification gradient boosting algorithm. However, there is no closed-form solution when optimizing $$\gamma_{jm}$$. We approximate it by a single Newton-Raphson step.

    We could futher extend the logistic binomial gradient boosting to multiclass logistic regression and classification problems.

## Regulirization 

In prediction problems, regularization methods attempt to prevent "overfitting" by constraining the fitting procedure. In additive models, two natural regularization parameters are the number of components $$M$$ and learning rate $$\nu$$.

- $$M$$
    - Regularizing by controlling the number of terms in the additive expansion places a prior belief that "sparse" approximations involving fewer terms are likely to provide better prediction. This is analogous to stepwise regression where the additive components are considered explanatory variables that are sequentially entered. 

    - The best $$M$$ can be estimated by some model selection method, such as train/test split or cross-validation.
- $$\nu$$:
    - It has often been found that regularization through shrinkage provides superior results to that obtained by restricting the number of components. In ***Algorithm 1***, each update is simply scaled by the value of the "learing rate" paratemer $$\nu$$.
    

Both of these two regularization parameters can control the degree of fit and thus affect the best value for the other one. Decreasing the value of $$\nu$$ increases the best value for $$M$$. Ideally one should estimate optimal values for both by minimizing a model selection criterion jointly with respect to the values of the two parameters. There are also computational considerations; increasing the size of $$M$$ produces a proportionate increase in computation. 




### Tree Boost








# New Features!

  - Import a HTML file and watch it magically convert to Markdown
  - Drag and drop images (requires your Dropbox account be linked)
- item
    - item 
        - item 

You can also:
  - Import and save files from GitHub, Dropbox, Google Drive and One Drive
  - Drag and drop markdown and HTML files into Dillinger
  - Export documents as Markdown, HTML and PDF

Markdown is a lightweight markup language based on the formatting conventions that people naturally use in email.  As [John Gruber] writes on the [Markdown site][df1]

> The overriding design goal for Markdown's
> formatting syntax is to make it as readable
> as possible. The idea is that a
> Markdown-formatted document should be
> publishable as-is, as plain text, without
> looking like it's been marked up with tags
> or formatting instructions.

This text you see here is *actually* written in Markdown! To get a feel for Markdown's syntax, type some text into the left window and watch the results in the right.

### Tech

Dillinger uses a number of open source projects to work properly:

* [AngularJS] - HTML enhanced for web apps!
* [Ace Editor] - awesome web-based text editor
* [markdown-it] - Markdown parser done right. Fast and easy to extend.
* [Twitter Bootstrap] - great UI boilerplate for modern web apps
* [node.js] - evented I/O for the backend
* [Express] - fast node.js network app framework [@tjholowaychuk]
* [Gulp] - the streaming build system
* [Breakdance](https://breakdance.github.io/breakdance/) - HTML to Markdown converter
* [jQuery] - duh

And of course Dillinger itself is open source with a [public repository][dill]
 on GitHub.

### Installation

Dillinger requires [Node.js](https://nodejs.org/) v4+ to run.

Install the dependencies and devDependencies and start the server.

```sh
$ cd dillinger
$ npm install -d
$ node app
```

For production environments...

```sh
$ npm install --production
$ NODE_ENV=production node app
```

### Plugins

Dillinger is currently extended with the following plugins. Instructions on how to use them in your own application are linked below.

| Plugin | README |
| ------ | ------ |
| Dropbox | [plugins/dropbox/README.md][PlDb] |
| GitHub | [plugins/github/README.md][PlGh] |
| Google Drive | [plugins/googledrive/README.md][PlGd] |
| OneDrive | [plugins/onedrive/README.md][PlOd] |
| Medium | [plugins/medium/README.md][PlMe] |
| Google Analytics | [plugins/googleanalytics/README.md][PlGa] |


### Development

Want to contribute? Great!

Dillinger uses Gulp + Webpack for fast developing.
Make a change in your file and instantaneously see your updates!

Open your favorite Terminal and run these commands.

First Tab:
```sh
$ node app
```

Second Tab:
```sh
$ gulp watch
```

(optional) Third:
```sh
$ karma test
```
#### Building for source
For production release:
```sh
$ gulp build --prod
```
Generating pre-built zip archives for distribution:
```sh
$ gulp build dist --prod
```
### Docker
Dillinger is very easy to install and deploy in a Docker container.

By default, the Docker will expose port 8080, so change this within the Dockerfile if necessary. When ready, simply use the Dockerfile to build the image.

```sh
cd dillinger
docker build -t joemccann/dillinger:${package.json.version} .
```
This will create the dillinger image and pull in the necessary dependencies. Be sure to swap out `${package.json.version}` with the actual version of Dillinger.

Once done, run the Docker image and map the port to whatever you wish on your host. In this example, we simply map port 8000 of the host to port 8080 of the Docker (or whatever port was exposed in the Dockerfile):

```sh
docker run -d -p 8000:8080 --restart="always" <youruser>/dillinger:${package.json.version}
```

Verify the deployment by navigating to your server address in your preferred browser.

```sh
127.0.0.1:8000
```

#### Kubernetes + Google Cloud

See [KUBERNETES.md](https://github.com/joemccann/dillinger/blob/master/KUBERNETES.md)


### Todos

 - Write MORE Tests
 - Add Night Mode

License
----

MIT


**Free Software, Hell Yeah!**

[//]: # (These are reference links used in the body of this note and get stripped out when the markdown processor does its job. There is no need to format nicely because it shouldn't be seen. Thanks SO - http://stackoverflow.com/questions/4823468/store-comments-in-markdown-syntax)


   [dill]: <https://github.com/joemccann/dillinger>
   [git-repo-url]: <https://github.com/joemccann/dillinger.git>
   [john gruber]: <http://daringfireball.net>
   [df1]: <http://daringfireball.net/projects/markdown/>
   [markdown-it]: <https://github.com/markdown-it/markdown-it>
   [Ace Editor]: <http://ace.ajax.org>
   [node.js]: <http://nodejs.org>
   [Twitter Bootstrap]: <http://twitter.github.com/bootstrap/>
   [jQuery]: <http://jquery.com>
   [@tjholowaychuk]: <http://twitter.com/tjholowaychuk>
   [express]: <http://expressjs.com>
   [AngularJS]: <http://angularjs.org>
   [Gulp]: <http://gulpjs.com>

   [PlDb]: <https://github.com/joemccann/dillinger/tree/master/plugins/dropbox/README.md>
   [PlGh]: <https://github.com/joemccann/dillinger/tree/master/plugins/github/README.md>
   [PlGd]: <https://github.com/joemccann/dillinger/tree/master/plugins/googledrive/README.md>
   [PlOd]: <https://github.com/joemccann/dillinger/tree/master/plugins/onedrive/README.md>
   [PlMe]: <https://github.com/joemccann/dillinger/tree/master/plugins/medium/README.md>
   [PlGa]: <https://github.com/RahulHP/dillinger/blob/master/plugins/googleanalytics/README.md>
