---
layout: post
title:      "Understanding ACF and PACF Plots"
date:       2019-10-16 10:52:34 -0400
permalink:  understanding_acf_and_pacf_plots
---

Time series forecasting is a machine learning model used for time series data compiled hourly, daily, weekly etc. to predict future, meaningful values based on previously observed values. There are several statistical models that help us forecast data and one of the most common models is the ARIMA model. ARIMA stands for Auto Regressive Integrated Moving Average and has three parameters (p,d,q) to account for trends, seasonality and noise in the data. One of the ways to predict these parameters is the use of two graphs: ACF and PACF.

 The goal of this post is to understand the intuition behind these plots in a time series analysis. Most of us know we must use ACF & PACF plots to determine the p and q values to create the ARIMA model but may not understand why we use ACF and PACF to obtain the p and q values respectively and not the other way around. 
 
First, we need to understand what ACF & PACF plots are:
ACF is the complete auto-correlation function which gives us the value of the autocorrelation of any series with lagged values. In other words, it describes how well present values are related to its past values. When we plot these values along with a confidence band, we create an ACF plot. A time series has several components which includes seasonality, trend, cyclic and residual. The ACF takes all of these into account while finding correlations which is why it is the complete auto-correlation plot.

PACF is the partial auto-correlation function. Unlike ACF, the PACF finds correlations with the residuals (these are the values that remain after removing the other effects) with the next lag. So, if there is any remaining information which can be modeled by the next lag, we might get a good correlation and we will keep that lag as a feature when modelling. When we predict a model, we do not want too many features which can create multicollinear issues. Therefore, we only keep the relevant features.

Now, we will focus on the AR & MA time series process:
A model is said to be auto regressive if the present value of the time series can be obtained using the previous values of the same time series i.e., using the past monthly returns of housing to determine the present monthly returns.
The AR process of order lag p is found using:
                                                          y = c +Φt-1*yt-1 + Φt-2*yt-2+...+Φt-p*yt-p+ε(t)
Ε(t) is the white noise and the y_t-1 and y_t-2 are the lags. Order p is the lag value after which the PACF plot passes the upper confidence band for the first time. These p lags will act as the number of features used to forecast the AR time series. We can't use the ACF plot for this process because it will show good correlations for lags well into the past. If we use this plot, the model will have too many features and multicollinear issues will arise. This is not an issue for PACF plots because it removes components already explained by earlier lags, so we only get lags that are correlated to the residuals.

For example, in the Zillow time series analysis that I recently conducted for Grand Prarie, TX, I found the order of the AR order from a PACF plot: 
```
from statsmodels.graphics.tsaplots import plot_acf, plot_pacf
def acf_pacf(df, alags=48, plags=48):
    #Create figure
    fig,(ax1, ax2) = plt.subplots(2,1, figsize=(13,8))
    #Create ACF plot
    plot_acf(df, lags=alags, zero=False, ax=ax1)
    #PACF plot
    plot_pacf(df, lags=plags, ax=ax2)
    plt.show()
acf_pacf(TS_75052)
```
 https://www.dropbox.com/s/0z9m8z436f8s688/Screenshot%202019-10-16%2009.57.11%20%28edited-Pixlr%29.jpg?dl=0
From this PACF plot, we can see a statistically significant lag at 0 or the present value and another lag at one or two so the AR’s p order is one. Most of the other lags are not significant because they are within the confidence band.

Now we will discuss the second part of the process:
A model is said to be a moving average process if the present values of the series is defined as a linear combination of past errors. We assume errors to be independently distributed with the normal distribution. The MA process is defined as:
y_t = c + εt + Θt-1*εt-1 + Θt-2*εt-2+...+Θt-q*εt-q
εt is the white noise, c is the mean value and theta is some coefficient. Order q of MA is obtained using the ACF plot, this is where the lag passes the confidence band for the first time. In a MA process there isn't any seasonal or trend components so the ACF plot will capture the correlations due to residuals only. In the below code, I plotted an ACF plot to find the MA for the Zillow time series analysis. 
https://www.dropbox.com/s/0z9m8z436f8s688/Screenshot%202019-10-16%2009.57.11%20%28edited-Pixlr%29.jpg?dl=0![
 
From this ACF plot, we can see that there is one positive statistically significant lag and two negative statistically significant lag, so the MA’s q order is one.

Like multiple linear regression, we need to use only the relevant and optimal number of features in a time series model. We find the optimal number of features in an AR process using PACF because it removes variations explained by earlier lags. We find the optimal number of features in the MA process using ACF because the MA process doesn't have seasonal or trend components, so we only get the residual relationship with the lags in an ACF plot. In this case, the ACF plot acts as a partial plot.

