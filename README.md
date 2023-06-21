

<h1 align="center">Fitting large satellite, right-censored failure data with covariates to the Weibull distribution</h1>




<h2 align="center">Introduction</h2> 
	Satellites are becoming integral to functions of many different industries (Brukardt, 2022).  On pace to become a $1 trillion industry, satellites have practical uses of analyzing climate change, food security, and national security while also being a part of the agricultural, energy, mining, insurance, and internet services industries (Brukardt, 2022).  The factor tying these industries and how they all use satellites is geographic information systems (GIS), as satellites record a variety of electromagnetic wavebands reflecting from large areas without having to be near that area. 
 <br>
 <br>
	Reliability within GIS, though, has not matched the mathematical field of reliability; rather, it has focused on the adequacy of geospatial (Blais, 2002). There has been some satellite reliability analysis that is geared specifically towards the space industry (Saleh & Castet, 2009). This paper will apply reliability analysis to satellites, emphasizing the importance not for the space industry but for industries setting up analysis pipelines dependent on satellites. By having an effective manner of analyzing reliability, these industries can build pipelines, leveraging satellite imagery, and be cognizant of planning around satellite failure. 
 <br>
 <br>
 This article leverages the Weibull distribution to fit the distribution of large and medium sized satellite failures, accounting for covariates to be discussed later, on publicly available data. The hope is that this methodology will be intuitive and accessible for non-space industries planning to build analytical infrastructure around satellites. 
 
<h2 align="center">Literature Review</h2> 
 
 	The literature on the reliability of literature is limited. Saleh and Castet have built a sizable share of the existing literature on satellite reliability. In their initial analysis in 2009, incorporating Type IV censoring, Saleh and Castet relied on the following parameterization of intensity function: \lambda\left(t\right)=\frac{\beta}{\theta}\left(\frac{t}{\theta}\right)^{\beta-1}. Here, the shape parameter is \beta and the scale parameter is \frac{1}{\theta}. Saleh and Castet assert that \hat{\beta}=\ .3875 with an average lifetime of  \hat{\theta}=8316 years, however they do not provide the standard error of the parameters, so it is unclear whether the exponential distribution is not the true distribution here. This analysis is important because it began research into satellite reliability, but it did not account for covariates; rather, it focused the data to different subsystems and looked at the reliability of those subsystems. Saleh and Castet extended this study in the following year by looking at multi-state failure, but the methodology, for the most part, remained the same (2010).
 <br>
 <br>
 There have been attempts to account for variation within different types of satellites (Guo et al., 2014). However, these attempts treated the variations in satellite type similar to how Saleh and Castet treated different subsystems – by looking at the reliability of those different variations individually from one another and comparing them (Guo et al., 2014; 2010). For instance, Guo et al., focusing on small satellites, compared the reliability of different launch years, mission types, and types of satellites (2014). 
 <br>
 <br>
 Both Guo et al. and Saleh and Castet found that there was a high infant mortality (2014; 2010). This is important because it indicates that much of the failures are right at the beginning, making the distribution lopsided. This is intuitive, though, because the riskiest part of their lifetime is right when they take off. Once satellites arrive in their destination, the satellite can remain relatively stable, barring crashes with other objects or re-entering the earth’s atmosphere due to gravity. 
 <br>
 <br>
 Though both studies noticed a high infant mortality rate among the satellites, they pursued different methodologies to get to that conclusion. Guo et al. used the widely known non-parametric Kaplan-Meier modeling technique (2014). This can be beneficial when certain assumptions cannot be met when fitting a distribution or when dealing with data scarcity (Guo et al., 2014). Guo et al. noted that this research area is limited because either researchers can fit data to a distribution where the data contains many unaccounted variations or use a refined dataset with little variations on a non-parametric method without extending the results to the rest of the population. This paper will later demonstrate that these are not the only two options. 
 <br>
 <br>
 The last paper for this topic was produced by Wang and Zhou in 2021. Like Saleh and Castet, Wang and Zhou fit right censored data to the Weibull (2021). However, they leveraged a 2-Weibull segmented model, which separated the data points into the initial high failure rate and the later stable portion of satellite life, while incorporating a Hough transformation algorithm. The results of this study were similar to the previous three.
 <br>
 <br>
 There are two key gaps in the literature. First, the literature, does not incorporate the relationship that covariates have on the lifetime of the satellites within the Weibull distribution fit. This is arguably important because industries could be more efficient with a single streamlined model, including all covariates, rather than needing to pull from many different models for each covariate. The next gap is that the literature did not write the standard errors of their parameters. Because of this, the exponential distribution cannot be discounted as the better fit for the satellite failures. 
 
<h2 align="center">Methodology</h2> 
	The data used for this analysis was from the online, freely available dataset Space-Track which is hosted by the United States’ Military. This dataset contains all publicly available information on satellites sent into orbit (even if they fail the launch). The covariates used from this dataset are as follows: period – the number of minutes till one full orbit, perigee – the closest distance that the satellite will be from the earth in kilometers, apogee – the farthest distances that the satellite will be from the earth in kilometers, inclination – the angle between the equator and the orbit. 

<br>
 <br>
To minimize potential variation between satellites, the data was specified to only look at large or medium satellites, as these would likely have more complex or potentially dangerous material if they were to collide/fall. These satellites were further specified to only look at launches from the Air Force Eastern Test Range and Air Force Western Test Range within the United States. This accounts for regulatory variation between agencies and countries as that can impact satellite reliability (Guo et al., 2014). To account for technological advances, I picked up the timeline from the end of Saleh and Castet’s first analysis. I planned to closely resemble this methodology, so I only looked at satellites that were launched after January 1st , 2010 and before April 10th, 2023, at 4:10pm eastern time. This subsetting process resulted in 4608 datapoints on satellite failure with 4265 right censored datapoints. 
 <br>
 <br>
 
 Because the data was a staggered right censored (Type IV), there was the potential that some started after 2010 or failed before 2023. To adjust for this, I changed the variables of the launch and decay columns to date-time datatypes with the Python Pandas package. I then created an “event” column where 0 indicates that there was censoring and 1 indicates there was no censoring (i.e. we witnessed decay). Likewise, if there was no decay date, I then manually put in April 10th , 2023 as the censoring date. From there, I let t\ = time between launch and failure, measured in days. In pseudo-code, this is “time =  decay – launch”, as the decay date will always come after the launch date. After these transformations, the data is ready for analysis.
 <br>
 <br>
 The analysis was conducted in R, relying on the packages survival and SurvRegCensCov. The latter is critical because it builds upon the survival package to account for right censored failures with covariates, providing the point estimates and standard error for both. I relied on the work of Zhang in 2016 who wrote an article detailing the use cases for this package, and stepping through the functions necessary. To fit all the parameters, the maximum likelihood estimation (MLE) technique was used (Zhang, 2016). This is an expansion of the same methodologies noted in the Saleh and Castet (2009). More can be read about the baseline distribution fitting process for censored data in chapter 8 of the book Reliability: Probabilistic Models and Statistical Methods Third Edition by Lawrences Leemis (2023). 
 <br>
 <br>
 The parameterization of the baseline hazard function used by Zhang was h\left(t\right)=\lambda\gammat^{\lambda-1} where \lambda is the shape parameter and \gamma is the scale. Parameterization shifts based on the study, so I will rely on this one since it is how the R package SurvRegCensCov was built. I will refer to the parameters as shape and scale. 
 <br>
 <br>
 The analysis takes the following steps. First, I created a survival object with the Surv function where I set time, censoring event, and the type of censoring to the correct inputs. From there, used the survreg function to create regression model with the survival object being dependent on the covariates, listing the distribution as Weibull. After that, I fed the ConvertWeibull function the regression summary and a confidence level. I let \alpha=.05. This function returns the confidence bounds for each of the parameters. I recorded this to get any relevant results and to validate the range of lambda and kappa. 
 <br>
 <br>
 For validation, I rely on a visual check for the goodness of check with a failure histogram and a probability density graph using the models MLE parameters. Note, I used the square root of the length of the dataset as to create the number of bins in the histogram. 
 <br>
 <br>
 At the end, the industries should rely on a model in which there is not too much discrepancy between parametric and nonparametric fits as well as significant covariate impacts.

<h2 align="center">Results</h2> 
 For the results, I will present screenshots of the R output so that users can understand the function calls used to get there as well as graphs for a goodness of fit check. 
Figure 1: Weibull Fit Summary and Parameter Estimates

 
Figure 2: Shape and Scale 95% Confidence Interval
 
Figure 3: Satellite Failure Histogram
 
 
Figure 4:  Weibull Probability Density Plot with MLE Parameters
 
<h2 align="center">Discussion</h2> 
	This study witnesses a high satellite infant mortality, per figure 3 and the shape parameter being less than 1. This result aligns with the studies mentioned earlier, but the shape parameter here is considerably lower than those fitted to the Weibull (Saleh & Castet, 2009; Saleh & Castet, 2010; Guo et al., 2014; Wang & Zhou, 2021). Likewise, those studies are validated here because the 95% confidence interval for the shape parameter does not include 1, meaning with a 95% confidence level, the distribution is not exponential. Unfortunately, due to the choice of using days as the unit of measure rather than years and having a high number of datapoints, the mean lifetime was not tractable to calculate in R. Though from the graphs, once a satellite went up and maintained operational status greater than 3 years, it remained stable in its reliability; however, that second peak in the histogram is partly caused by right censoring. Regardless, it could be the case that this certain distribution is the incorrect fit because of those two clear peals, so Guo et al. could be correct that Weibull assumptions are not met here (2014); however, that is not to say that a non-parametric fit is the best solution, as to be discussed in the conclusion. 
 <br>
 <br>
 The covariates all received a significant p-value of less than .05. This indicates that the covariates are not equal to 0 and influence the lifetime of the satellite reliability model. The following interpretations can be deduced from the covariate estimates in Figure 1. Increasing the period, i.e. how long the satellite takes to complete a revolution, speeds up the failure process. Increasing the inclination of the satellite slows down the failure process. The results, though, indicate that increasing the perigee and apogee both slow down the failure process, meaning increasing the shortest and longest distance from the earth slows down the failure process. This could arguably be because of the distance from other orbiting satellites or the less pull from the earth’s gravity back into the atmosphere. 
Conclusion	 
 <br>
 <br>
 Satellite reliability is critical for industries building analysis pipelines that rely on data produced by satellites. The practical importance of this study is for these industries to have a data alternative up to the second or third year that a satellite is available. Once it hits that mark, the stability is more ensured, and the industries would only need to check for the satellite companies’ updates relating to when the satellite will be decommissioned. Likewise, these expectations can be adjusted relative to the desired satellite’s covariates. 
 <br>
 <br>
 A key limitation in this study was the mathematical intractability of the Weibull distribution when fitting it to right-censored data with covariates. However, mainly relying on the histogram, future research should expand beyond the Weibull distribution as to account for the different waves of failure (again, this is partially caused by censoring). This could potentially entail leveraging a non-homogeneous Poisson process or a more tractable mathematical model. Future researchers, though, should be cautious about relying on tractability over including covariates, as evidence suggests that they have an effect. 
 
<h2 align="center">References</h2> 
Brukardt, R. (Nov. 2022). How will the space economy change the world? McKinsey Quartley. https://www.mckinsey.com/industries/aerospace-and-defense/our-insights/how-will-the-space-economy-change-the-world#/
 <br>
 <br>
Castet, J. F. & Saleh, J. H. (Nov. 2010). Beyond reliability, multi-state failure analysis of satellite subsystems: a statistical approach. Reliability Engineering and System Safety, 95, 311-322. doi: 10.1016/j.ress.2009.11.001
 <br>
 <br>
Guo, J., Monas, L., & Gill, E. (Jan. 2014). Statistical analysis and modelling of small satellite reliability. Acta Astronautica, 98, 97-110. http://dx.doi.org/10.1016/j.actaastro.2014.01.018
 <br>
 <br>
Leemis, L. (2023). Reliability: probabilistic models and statistical methods. ISBN: 978-0-9829174-4-2
 <br>
 <br>
Saleh J. H. & Castet, J.F. (Oct. 2009). Satellite reliability: statistical data analysis and modeling. Journal of Spacecraft and Rockets, 46(5). DOI: 10.2514/1.42243.
 <br>
 <br>
Space-Track. [online database]. SAIC. https://www.space-track.org. 
 <br>
 <br>
Wang, J. & Zhou, C. (Sept. 2021). Statistical modeling and analysis of satellite failure based on 2-weibull segmented model. IEEEAccess, 9, 128747-128754. DOI: 10.1109/ACCESS.2021.3113155




