# CyclePower
  A machine learning approach to factors affecting output of a power plant
  At the Colorado School of Mines, we would occasionally take a dive into fluid dynamics or heat transfer problems where regression of environmental factors affecting performance was to be solved.   The problem in question here is a two cycle power plant, where a bank of gas turbines generate electricity, while driving steam turbines downstream.  Data collected included Ambient temperature (AT), Exhaust Vacuum (V),  Ambient Pressure (AP), relative Humidity (RH), and the power output (PE).   From the Kaggle problem statement, the vacuum V is collected at the second (steam) stage.   
  The dataset is from Kaggle, but is nearly identical to problems solved while at Colorado School of Mines.   Back in those days, we didn't have nice R packages or machine learning to find the proper regression relationship between power output and the environmental variables - it had to be estimated by hand.   Let's use R to see if we can improve on the solution:
  An ideal regression analysis should look at: 1. Linearity of the relationship 2. Homoscedasticity (variance from the regression line should not change much along the range of the line) 3. Multi-variate Normality, 4, Independence of errors, and 5. lack of Colliniearity.   I am primarily interested in producing the best fit equation, based on root squared error (RSE), the adjusted R-squared, the F statistic, and the AIC (Akaike Information criterion).  We will check the p scores for statstical significance and the AIC carefully.    For this quick-pass study, I will not be involving a proof of 1 thru 5 above in detail, but R can help us make visual judgements from the Q-Q and variance plots, whether we are violating any assumptions above.   Using R, we can look at colinearity as well.
  
We took five variations of input parameters, and collected some performance metrics on each:
1. PE ~ AT + AP + RH.  The problem statement from Kaggle says that exhaust Vacuum V is collected from the end of the steam loop.  Let's take it out:
        RSE = 4.77  R-sqr = 0.92    F = 2.8x10^4    AIC = 42896.88
        From the coeff matrix (not shown) AP is the least important, with a high p value, and a much smaller coeff contribution.  We will note that.....
2. PE ~ AT + AP + RH + V.  Add vacuum back in: 
RSE = 4.60  R-sqr = 0.93    F = 2.3x10^4    AIC = 42128.75
        Here, the RSE has gone down, more of the variation is explained by the regression model.   The F, which is really the sum of squares of the errors of regressors, divided by the mean squared errors of the data itself, has gone down - we need to keep an eye on that.   But the Akiake criterion,  which suggests a better fit for future data.   Let's use backward elimination to remove the pesky AP:
3. PE ~ AT + RH + V.    AP removedL
RSE = 4.66  R-sqr = 0.93    F = 3.076x10^4  AIC = 42156.76    
        The F has gone up, and AIC increased, not a good outcome.  Something is going on.  Running cor(x) function in R, we find V and AT to be highly autocorrelated (cor = 0.844).   We have multi-collinearity:
4. PE ~ AT + AP + V + AT:V    Cross effect AT:V added into regression now:
RSE = 4.60  R-sqr = 0.97    F = 2.27x10^4   AIC = 42257.16
        The AIC and R squared have gotten worse.  And the AT:V coefficient is small -- 0.02.   Perhaps this was a bad choice.  Let's simplify the model to account for AT and V only:
5. PE ~ AT + V  cross correlation and AP removed:
RSE = 4.95  R-sqr = 0.92    F = 3.87x10^4   AIC = 43308.     Definitely a wrong move; RSE has increased, the F has increased (more error is explained by our model picks) and the AIC has increased (suggesting future predictions will be less accurate). The model is less powerful.

