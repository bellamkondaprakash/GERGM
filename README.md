# GERGM -- Master: [![Travis-CI Build Status](https://travis-ci.org/matthewjdenny/GERGM.svg?branch=master)](https://travis-ci.org/matthewjdenny/GERGM) [![CRAN_Status_Badge](http://www.r-pkg.org/badges/version/GERGM)](http://cran.r-project.org/package=GERGM) Development: [![Travis-CI Build Status](https://travis-ci.org/matthewjdenny/GERGM.svg?branch=Development)](https://travis-ci.org/matthewjdenny/GERGM)
An R package to estimate Generalized Exponential Random Graph Models. To get started, **[check out this vignette!](http://www.mjdenny.com/getting_started_with_GERGM.html)**

**PLEASE REPORT ANY BUGS OR ERRORS TO <mdenny@psu.edu>**. 

## News

**[09/17/16]** A major update to the package has been pushed to the public GERGM repo and will shortly be up on CRAN. Here are some highlights:

* **correlation networks**: We have added functionality to estimate GERGMS on correlation networks via the `beta_correlation_model` parameter. 
* **updated conditional edge prediction** The package now offers updated functions for conditional edge prediction and comparison to a null model in terms of edgewise MSE. 
* **[a "getting started with GERGM" vignette](http://www.mjdenny.com/getting_started_with_GERGM.html)** now includes lots more information on estimating a simple model and generating diagnostic plots. 


**[08/06/16]** A major update to all aspects of the package has been pushed to the public GERGM repo and will shortly be up on CRAN. **Note that this version does break compatibility with estimation results generated by the previous version of the package.** Here are some highlights:

* **parallelization**: We have added a number of different methods of parallelization to the package, both in initialization, and in statistic calculation.
* **conditional edge prediction**: The package now provides functionality to predict edge values from a GERGM fit.
* **updated diagnostic plots**: The package now calculates additional statistics and provides node degree density plots.
* **node subset statistics**: All endogenous statistics (ttriads, etc.) can now be calculated for subsets of nodes in the network, greatly expanding the number of possible specifications.
* **explicit intercept specification**: You are now required to explicitly include an intercept term in models.
* **slackR integration**: You can now have the `gergm()` function send you updates about estimation to a designated slack channel. This is as close to having the package text message users as we could get. Useful for keeping up to date on long running models. 
* **estimation without endogenous statistics**: You may now estimate models without any endogenous statistics (covariate effects only), while still having access to all goodness of fit diagnostics.

## Model Overview 

An R package which implements the Generalized Exponential Random Graph Model (GERGM) with an extension to estimation via Metropolis Hastings. The relevant papers detailing the model can be found at the links below:

* Bruce A. Desmarais, and Skyler J. Cranmer,  (2012). "Statistical inference for valued-edge networks: the generalized exponential random graph model". PloS One. [[Available Here](http://dx.plos.org/10.1371/journal.pone.0030136)]
* James D. Wilson, Matthew J. Denny, Shankar Bhamidi, Skyler Cranmer, and Bruce Desmarais (2017). "Stochastic weighted graphs: Flexible model specification and simulation". Social Networks, 49, 37–47. [[Available Here](http://doi.org/10.1016/j.socnet.2016.11.002)]
* Matthew J. Denny (2016). "The Importance of Generative Models for Assessing Network Structure". [[Available Here](http://ssrn.com/abstract=2798493)]

To maximize translation across related methods, we have followed many naming conventions used in the [statnet](https://CRAN.R-project.org/package=statnet) suite of packages, and in the [ergm](https://CRAN.R-project.org/package=ergm) package particularly.

## Installation

### Requirements for using C++ code with R

See the **Requirements for using C++ code with R** section in the following tutorial: [Using C++ and R code Together with Rcpp](http://www.mjdenny.com/Rcpp_Intro.html). You will likely need to install either `Xcode` or `Rtools` depending on whether you are using a Mac or Windows machine before you can use the package.

### Installing The Package

The easiest way to do this is to install the package from CRAN via the standard `install.packages` command:

    install.packages("GERGM")

This will take care of some weird compilation issues that can arise, and is the best option for most people. If you want the most current development version of the package (available here), you will need to start by making sure you have Hadley Wickham's devtools package installed.

    install.packages("devtools")
    
Now we can install from Github using the following line:

    devtools::install_github("matthewjdenny/GERGM")

I have had success installing this way on most major operating systems with R 3.2.0+ installed, but if you do not have the latest version of R installed, or run into some install errors (please email if you do), it should work as long as you install the dependencies first with the following block of code:

    install.packages( pkgs = c("BH","RcppArmadillo","ggplot2","methods",
    "stringr","igraph", "plyr", "parallel", "coda", "vegan", "scales",
	"RcppParallel","slackr"), dependencies = TRUE)

Once the `GERGM` package is installed, you may access its functionality as you would any other package by calling:

    library(GERGM)

If all went well, check out the `?GERGM` help file to see a full working example with info on how the data should look. 

## Basic Usage

To use this package, first load in the network you wish to use as a (square) matrix, following the example provided below. You may then use the `gergm()` function to estimate a model using any combination of the following statistics: `out2stars(alpha = 1)`, `in2stars(alpha = 1)`, `ctriads(alpha = 1)`, `mutual(alpha = 1)`, `ttriads(alpha = 1)`,  `absdiff(covariate = "MyCov")`, `sender(covariate = "MyCov")`, `reciever(covariate = "MyCov")`, `nodematch(covariate)`, `nodemix(covariate, base = "MyBase")`, `netcov(network)`, and `edges(alpha = 1, method = c("regression","endogenous"))`. Note that the `edges` term must be specified if the user wishes to include an intercept (strongly recommended). The user may select the "regression" method (default) to include an intercept in the lambda transformation of the network, or "endogenous" to include the intercept as in a traditional ERGM model. To use exponential down-weighting for any of the network level terms, simply specify a value for alpha less than 1. The `(alpha = 1)` term may be omitted from the structural terms if no exponential down-weighting is required. In this case, the terms may be provided as: `out2stars`, `in2stars`, `ctriads`, `mutual`, `ttriads`, `edges`. 

If the network is undirected the user may only specify the following terms: `twostars(alpha = 1)`,  `ttriads(alpha = 1)`,  `absdiff(covariate = "MyCov")`, `sender(covariate = "MyCov")`, `nodematch(covariate)`, `nodemix(covariate, base = "MyBase")`, `netcov(network)`, and `edges(alpha = 1, method = c("regression","endogenous"))`. The `gergm()` function will provide all of the estimation and diagnostic functionality and the parameters of this function can be queried by typing `?gergm` into the R console. You may also generate diagnostic plots using a GERGM Object returned by the `gergm()` function by using any of the following functions: `Estimate_Plot()`, `GOF()`, `Trace_Plot()`. The user may also investigate the sensitivity of parameter estimates using the `hysteresis()` function. This function simulates large numbers of networks at parameter values around the estimated parameter values an plots the mean network density at each of these values to examine whether the model becomes degenerate due to small deviations in the parameter estimates. See the following reference for details:

* Snijders, Tom AB, et al. "New specifications for exponential random graph models." Sociological methodology 36.1 (2006): 99-153.

The user may also predict edge values (one at a time, conditioning on the rest of the edge values, and estimated parameters), using the `conditional_edge_prediction()` and `conditional_edge_prediction_plot()` functions.

## Examples

Here are two simple working examples using the `gergm()` function: 
    
    library(GERGM)
    ########################### 1. No Covariates #############################
    # Preparing an unbounded network without covariates for gergm estimation #
    set.seed(12345)
    net <- matrix(rnorm(100,0,20),10,10)
    colnames(net) <- rownames(net) <- letters[1:10]
    formula <- net ~ edges(method = "endogenous") + mutual + ttriads 
      
    test <- gergm(formula,
    	          normalization_type = "division",
    	          network_is_directed = TRUE,
    	          number_of_networks_to_simulate = 40000,
    	          thin = 1/10,
    	          proposal_variance = 0.2,
    	          MCMC_burnin = 10000,
    	          seed = 456,
    	          convergence_tolerance = 0.01,
    	          force_x_theta_update = 4)
      
    ########################### 2. Covariates #############################
    # Preparing an unbounded network with covariates for gergm estimation #
    set.seed(12345)
    net <- matrix(runif(100,0,1),10,10)
    colnames(net) <- rownames(net) <- letters[1:10]
    node_level_covariates <- data.frame(Age = c(25,30,34,27,36,39,27,28,35,40),
    	                                Height = c(70,70,67,58,65,67,64,74,76,80),
    	                                Type = c("A","B","B","A","A","A","B","B","C","C"))
    rownames(node_level_covariates) <- letters[1:10]
    network_covariate <- net + matrix(rnorm(100,0,.5),10,10)
    formula <- net ~  edges(method = "regression") + mutual + ttriads + sender("Age") + 
    netcov("network_covariate") + nodemix("Type",base = "A")  
       
    test <- gergm(formula,
    	          covariate_data = node_level_covariates,
    	          number_of_networks_to_simulate = 100000,
    	          thin = 1/10,
    	          proposal_variance = 0.2,
    	          MCMC_burnin = 50000,
    	          seed = 456,
    	          convergence_tolerance = 0.01,
    	          force_x_theta_update = 2)
      
    # Generate Estimate Plot
    Estimate_Plot(test)
    # Generate GOF Plot
    GOF(test)
    # Generate Trace Plot
    Trace_Plot(test)
    # Generate Hysteresis plots for all structural parameter estimates
    hysteresis_results <- hysteresis(test,
                                     networks_to_simulate = 1000,
                                     burnin = 500,
                                     range = 2,
                                     steps = 20,
                                     simulation_method = "Metropolis",
                                     proposal_variance = 0.2)
									 

### Edge Prediction

Following on from the example above, we can also predict individual edge values, conditioning on the rest of the observed edges and estimated parameters. These predictions can then be plotted as a grid of box plots for each individual edge, as in the following example code:
	
	test2 <- conditional_edge_prediction(
	  GERGM_Object = test,
	  number_of_networks_to_simulate = 1000,
	  thin = 1,
	  proposal_variance = 0.5,
	  MCMC_burnin = 1000,
	  seed = 123)
	
	# set working directory where we want to save the file. Only outputs a PDF
	# because plotting is too slow in the graphics device.
	setwd("~/Desktop")
	conditional_edge_prediction_plot(edge_prediction_results = test2,
	                                 filename = "test.pdf",
	                                 plot_size = 20,
	                                 node_name_cex = 6)

	# make a scatter plot of predicted vs observed edge values
	conditional_edge_prediction_correlation_plot(edge_prediction_results = test2)

	# set x and y limits manually
	conditional_edge_prediction_correlation_plot(edge_prediction_results = test2,
	                                             xlim = c(-10,10),
	                                             ylim = c(-10,10))

### Parallel GERGM Specification Fitting
    
There is also now functionality to run multiple `gergm()` model specifications in parallel using the `parallel_gergm()` function. This can come in very handy if the user wishes to specify the same model but for a large number of networks, or multiple models for the same network. Another useful (experimental) feature that can now be turned out is `hyperparameter_optimization = TRUE`, which will seek to automatically optimize the number of networks simulated during MCMC, the burnin, the Metropolis Hastings proposal variance and will seek to address any issues with model degeneracy that arise during estimation by reducing exponential weights if using Metropolis Hastings. This feature is generally meant to make it easier and less time intensive to find a model that fits the data well.  

    set.seed(12345)
    net <- matrix(runif(100,0,1),10,10)
    colnames(net) <- rownames(net) <- letters[1:10]
    node_level_covariates <- data.frame(Age = c(25,30,34,27,36,39,27,28,35,40),
                               Height = c(70,70,67,58,65,67,64,74,76,80),
                               Type = c("A","B","B","A","A","A","B","B","C","C"))
    rownames(node_level_covariates) <- letters[1:10]
    network_covariate <- net + matrix(rnorm(100,0,.5),10,10)

    network_data_list <- list(network_covariate = network_covariate)

    formula <- net ~ edges + netcov("network_covariate") + nodematch("Type",base = "A")
    formula2 <- net ~ edges + sender("Age") +
      netcov("network_covariate") + nodemix("Type",base = "A")

    form_list <- list(f1 = formula,
                      f2 = formula2)

    testp <- parallel_gergm(formula_list = form_list,
                            observed_network_list = net,
                            covariate_data_list = node_level_covariates,
                            network_data_list = network_data_list,
                            cores = 2,
                            number_of_networks_to_simulate = 10000,
                            thin = 1/100,
                            proposal_variance = 0.1,
                            MCMC_burnin = 5000)

Finally, if you specified an `output_directory` and `output_name`, you will want to check the `output_directory` which will contain a number of .pdf's which can aide in assessing model fit and in determining the statistical significance of theta parameter estimates.

### Output

If `output_name` is specified in the `gergm()` function, then five files will be automatically generated and saved to the `output_directory`. The example file names provided below are for `output_name = "Test"`:

* **"Test_GOF.pdf"**  -- Normalized, standardized Goodness Of Fit plot comparing statistics of networks simulated from final parameter estimates to the observed network.
* **"Test_Parameter_Estimates.pdf"** -- Theta parameter estimates with 90 and 90% confidence intervals.
* **"Test_GERGM_Object.Rdata"** -- The GERGM object returned by the `gergm()` functionafter estimation.
* **"Test_Estimation_Log.txt"** -- A log of all console output generated by the `gergm()` function.
* **"Test_Trace_Plot.pdf"** -- Trace plot from last round of network simulations used to generate GOF plot, useful for diagnosing an acceotance rate that is too low.

## Advanced Features

The GERGM package incorporates a number of advanced features that are designed to aid in model fitting and evaluation, and expand the applicability of the model to a wider range of networks and network statistics. 

### `gergm()` optional arguments 

* **force_x_theta_updates** -- This can be a useful way to improve model fit, or to assess the robustness of theta estimates. The standard way to make sure that theta estimates have converged is to set a large value for the `convergence_tolerance` parameter ( which defaults to 0.5). However, if the user suspects that the theta parameter estimates have not actually converged, despite the t-test p-values, then they can for the model to perform a certain number of theta updates. This is a particularly useful feature in helping to determine whether a specification suffers from likelihood degeneracy. The user can set the number of theta updates to be higher than the `convergence_tolerance` parameter threshold would suggest, and see if theta parameter estimates diverge. If they do not, this is some indication that the estimation procedure is stable. However, in general, it is advisable to leave this parameter set to its default value of 1.
* **force_x_lambda_updates** -- This parameter functions very similarly to the `force_x_theta_updates` parameter, but is primarily useful for improving model fit. It may be the case that the model detects convergence, but due to slight changes in the lambda parameters at their last update, model fit may be impacted. Setting a higer value for this parameter can force the model to really "settle down" before completing estimation. In general, it is advisable to leave this parameter set to its default value of 1.
* **hyperparameter_optimization** -- Turning on this option does a number of things which are meant to automate some of the process of model fitting. The main function of this option is to turn out Metropolis Hasting proposal variance optimization. If selected, the program will automatically seek to find a proposal variance that leads to an acceptance rate which is within five percent of `target_accept_rate`. This adaptive metropolis step is done before the main estimation step, so it will not interfere with the usability of the MH samples we gather. The second function of this option is to attempt to automatically deal with likelihood degeneracy through the use of exponential down-weights (when using Metropolis-Hastings for estimation). If the estimation procedure detects problems with estimation (theta estimates zooming off to positive or negative infinity), the alpha weights for each of the endogenous statistics will be set to 90 percent of its current value. Decreasing these weights has been found to make it computationally easier to fit a model, however, the user is strongly encouraged to set alpha weights themselves or use the `theta_grid_optimization_list` option to improve initialization.
* **stop_for_degeneracy** -- If the user specifies this option, then estimation will automatically hard-stop when degeneracy is detected. 
* **theta_grid_optimization_list** -- This highly experimental feature may allow the user to address model degeneracy arising from a suboptimal theta initialization. It performs a grid search around the theta values calculated via MPLE to select a potentially improved initialization. The runtime complexity of this feature grows exponentially in the size of the grid and number of parameters -- use with great care. This feature may only be used if hyperparameter_optimization = TRUE, and if a list object of the following form is provided:

		theta_grid_optimization_list = list(
		     grid_steps = 2, 
		     step_size = 0.5, 
			 cores = 2, 
			 iteration_fraction = 0.5). 
			 
    grid_steps indicates the number of steps out the grid search will perform, step_size indicates the fraction of the MPLE theta estimate that each grid search step will change by, cores indicates the number of cores to be used for parallel optimization, and iteration_fraction indicates the fraction of the number of MCMC iterations that will be used for each grid point (should be set less than 1 to speed up optimization). In general grid_steps should be smaller the more structural parameters the user wishes to specify. For example, with 5 structural parameters (mutual, ttriads, etc.), grid_steps = 3 will result in a (2*3+1)^5 = 16807 parameter grid search. Again this feature is highly experimental and should only be used as a last resort (after playing with exponential downweighting and the MPLE_gain_factor).
* **beta_correlation_model** -- Option specifying whether the input network is a correlation network. If set to TRUE, then beta regression and a Harry-joe transform are used to model the correlation structure. Works just like any other (undirected) GERGM specification.  
* **weighted_MPLE** -- Logical indicating whether the estimation routine should use MPLE with weights on statistic values (via numerical approximation). This is recommended if the user is specifying `alpha` weights on any endogenous statistics that are less than 1. It will tend to slow down MPLE, but is optimized for parallelization, so it can take advantage of the `parallel` option. 
* **parallel** -- Logical indicating whether any operations that are "embarrassingly parallel" should be calculated in parallel acros a number of cores specified by the `cores` parameter. Since the operations governed by this parameter are easy to parallelize, it is possible to realize a significant speedup in their calculation, particularly when using weighted MPLE for large networks. 
* **parallel_statistic_calculation** -- This option turns on or off parallelization via threading in the endogenous statistic calculation for the Metropolis Hastings procedure. This will only really show a significant speedup in large graphs, when the user has specified several endogenous terms to be calculated, as each will be calculated in parallel. This option will actually slow down estimation when used for networks smaller than about 40-50 nodes, as threading requires some computational overhead. 
* **cores** -- If `parallel = TRUE` and/or `parallel_statistic_calculation = TRUE`, this is the number of cores that will be allocated for parallelization. Don't set this to a number greater than the number of threads your computer can support. 
* **use_stochastic_MH** -- This is logical indicating whether a stochastic approximation to the h statistics should be used under Metropolis Hastings to determine whether proposals are accepted or rejected (in-between the samples we save to use for MCMCMLE). We currently do dyad/triad sampling with biased sampling towards dyads/triads whose value is close to the mean. This is EXTREMELY EXPERIMENTAL.
* **stochastic_MH_proportion** -- Percentage of dyads/triads to use for the stochastic approximation above, defaults to 0.25. This is basically untested, but probably needs to be higher to get a good enough approximation for smaller networks, although the hope is that it could be a smaller proportion as graph size grows. 
* **estimate_model** -- Logical indicating whether estimation should be performed or whether a GERGM object should be returned before estimation. Defaults to TRUE. Setting this to FALSE can be useful for diagnosing bugs.
* **slackr_integration_list** -- An optional list object that contains information necessary to provide updates about model fitting progress to a Slack channel (https://slack.com/) of your choosing. This can be useful if models take a long time to run, and you wish to receive updates on their progress (or if they become degenerate). Before you can get this option to work, you will need to turn on "web hook integration" for your slack channel. If you go to the `Chanel Settings` -> `Add an app or integration` pane and search for "web hook integration", you should be able to turn this option on. From there you will get an incoming webhook url that you will need to copy and save for use with the `gergm()` function. It is also probably advisable to create a separate channel for updates, as these may end up swamping `#general`. Once you have a channel and incoming webhook url, you will need to enter your information as a list argument in the `gergm()`.  The list object must be of the following form: 

        slackr_integration_list = list(
                model_name = "descriptive model name", 
                channel = "#yourchannelname", 
                incoming_webhook_url = "https://hooks.slack.com/services/XX/YY/ZZ")
	
    Note that the `model_name` is a way to distinguish multiple models that can simultaneously post information to the same slack channel if you so desire, because `model_name` will appear as the name of the poster on the slack channel. If all goes well, and the computer you are running the GERGM estimation on has internet access, your slack channel will receive updates when you start estimation, after each lambda/theta parameter update, if the model becomes degenerate, and when it completes running. This feature is still in development. 


## Testing
            
So far, this package has been tested successfully on OSX, CentOS 7, Ubuntu and Windows 7. Please email me at <mdenny@psu.edu> if you have success on another OS or run into any problems.

## Intellectual Property
Please note that the use of the correlation network estimation functionality in this package is protected from unlicensed commercial use in neuroimaging applications under a provisional pattent. The software itself is completely open source, and you are free to copy, modify it, and use it in other applications. Please contact <mdenny@psu.edu> for more information if your company is interested in commercial neuroimaging applications using the GERGM. 
