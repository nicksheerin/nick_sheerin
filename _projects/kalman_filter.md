---
title: "Kalman Filter Explorer"
layout: single-portfolio
excerpt: "<img src='../images/projects/kalman_filter/static.png' alt=''>"
collection: projects
order_number: 50
---

This project explores building the intuition behind the Kalman Filter and exploring how to create a filter in 1D and expanding to more sophisticated use cases.

<div align="center">
<img src="../../images/projects/kalman_filter/static.png">
</div>

# Table of Contents

1) Overall Summary
2) Basic Terminology & Equations
3) 1D Kalman Filter
4) 2D Kalman Filter
5) 3D Kalman Filter
6) Conclusion
7) References

# Overall Summary

A standard Kalman filter is a recursive algorithm that is used for estimating the state if a **linear** system. One reason that this filter has been widely used because of its ability to expand and define relationships between the "system" and "external sources".

The filter operates in two primary stages:

1) **Update Step:** incorporates new measurements by calculating the difference (i.e. innovation) between the predicted and observed measurements. The Kalman gain is computed to optimally correct the state estimation by blending model predictions with sensor measurements.

2) **Prediction Step:** uses a process/system model to predict the next state and based on previous estimates and also estimate uncertainty.

Now this might seem complicated right now, but I will try to build the intuition and explain the terminology throughout this project.

# Basic Terminology & Equations

There are a total of 5 equations we will refer back to throughout this project. Note: Equations 1-2 are part of the **prediction step** above and Equations 3-5 are for the **update step**.

**1) State Extrapolation Eq.**
**2) Covariance Extrapolation Eq.**
**3) Kalman Gain Eq.**
**4) State Update Eq.**
**5) Covariance Update Eq.**

To set the context, we are going to refer to an example as we build out the equations used. Imagine we are trying to measure the weight of a robotic humanoid. 

The 1^st^ equation we will look at is called the <u>State Extrapolation Equation</u> (otherwise known as Dynamic Model or State Space Model). This equation is the basis of the filter since it defines the dynamic model of the system / environment.

In this particular example, the dynamic model is "constant". The weight of the robot is not changing as we try to measure the weight through multiple iterations. Mathematically, this relationship can be defined as:

$$ 
\hat{x}_{n+1,n} = \hat{x}_{n,n}
$$

where $\hat{x}_{n+1,n}$ is predicted estimate and $\hat{x}_{n,n}$ is the current estimate. 

Since the Kalman filter treats the estimate as a random variable (i.e. represents the hidden state of a system that is being estimated, and its values are uncertain and influenced by random noise but follow Gaussian properties), we must also [extrapolate](https://en.wikipedia.org/wiki/Extrapolation) the "estimation variance/uncertainty". Hence the 2^nd^ equation is called the <u>Covariance Extrapolation Equation</u>. In real-world applications there are uncertainties in the system dynamic model. Example, the sensor used to measure the weight of an object can be influenced by temperature. Hence, the uncertainty of the dynamic model is called the **process noise ($q_n$)**. Mathematically, the relationship is defined as:

$$ 
\hat{p}_{n+1,n} = \hat{p}_{n,n} + q_n
$$

where $\hat{p}_{n+1,n}$ is predicted estimation uncertainty and $\hat{p}_{n,n}$ is the current estimation uncertainty.

Now both of these equations assumed constant dynamics, but that might not be actually how a system behaves. For example, we might be interested in tracking the robot as it walks (i.e. we might want to estimate position and velocity). For brevity, let's assume constant velocity as the robot is walking in a straight line. We need to expand the dynamic model using motion equations for both position and velocity:

$$ 
\hat{x}_{n+1,n} = \hat{x}_{n,n} + \Delta{t}\hat{\dot x}_{n,n}
$$

$$
\hat{\dot x}_{n+1,n} = \hat{\dot x}_{n,n}
$$

where the predicted position ($\hat{x}_{n+1,n}$) equals the current estimated position ($\hat{x}_{n,n}$) plus the change in time multiplied by the estimated velocity ($\hat{\dot x}_{n,n}$). The predicted velocity ($\hat{\dot x}_{n+1,n}$)  equals the current velocity ($\hat{\dot x}_{n,n}$) estimate (assuming a constant velocity term, but again this could change if we had an acceleration term). Similarly, the covariance extrapolations equations would also need to be adjusted:

$$ 
\hat{p}^{pos}_{n+1,n} = \hat{p}^{pos}_{n,n} + \Delta{t^2}\hat{p}^{vel}_{n,n}
$$

$$
\hat{p}^{vel}_{n+1,n} = \hat{p}^{vel}_{n,n} + q_n
$$

Note that the above equations are part of the **prediction step**. Let's know dive into the **update step**.

The Kalman gain ($K_{n}$) is a measure of how much influence a new measurement should have on the state estimate, by balancing the uncertainties in the predictions and measurements. Mathematically, the <u>Kalman Gain Equation</u> can be defined as:

$$
K_{n} = \frac{\hat{p}_{n,n-1}}{\hat{p}_{n,n-1} + r_n}
$$

where $\hat{p}_{n,n-1}$ is the extrapolated estimate uncertainty / variance and $r_{n}$ is the measurement uncertainty / noise. Note, the Kalman Gain is a number that ranges between 0 and 1. When $K_{n}$ is close to 0 (**low Kalman gain**), the filter trusts the prior estimate more (i.e. high measurement uncertainty relative to the estimate uncertainty). When $K_{n}$ is close to 1 (**high Kalman gain**), the filter trusts the measurement more (i.e. low measurement uncertainty relative to the estimate uncertainty). If both uncertainties are equal then $K_{n} = 0.5$ giving equal weight to both the measurement and the estimate.

Now that this Kalman Gain has been derived, this variable can be used to update the state and covariance equations. The Kalman Filter is an optimal filter. It combines the prior state estimate with the measurement in a way that minimizes the uncertainty of the current state estimate. Mathematically, the <u> State Update Equation </u> is defined as:

$$ 
\hat{x}_{n,n} = \hat{x}_{n,n-1} + K_{n}(z_{n} - \hat{x}_{n,n-1})
$$

where $\hat{x}_{n,n}$ is the current estimate, $\hat{x}_{n,n-1}$ is the previous estimate, $K_{n}$ is the Kalman gain, and $z_{n}$ is the current measurement. Sometimes, the $(z_{n} - \hat{x}_{n,n-1})$ term is referred to as the measurement residual or the **innovation**. Lastly, the <u> Covariance Update Equation </u> is defined as:

$$ 
\hat{p}_{n,n} = (1 - K_{n})\hat{p}_{n,n-1}
$$

where $\hat{p}_{n,n}$ is the current estimation uncertainty and $\hat{p}_{n,n-1}$ is the previous estimation uncertainty.

## Quick Recap

The Kalman Filter is a recursive algorithm that are based on 5 equations:

1) <u> State Extrapolation Equation</u>: predicting or estimating the future state based on the known present estimation.
2) <u> Covariance Extrapolation Equation</u>: the measure of uncertainty in our prediction.
3) <u> State Update Equation</u>: estimating the current state based on the known prior estimation and present measurement.
4) <u> Covariance Update Equation</u>: the measure of uncertainty in our estimation.
5) <u> Kalman Gain Equation</u>: a “weighting” parameter between prior estimate versus the new measurement.

The Kalman filter is recursive because at each cycle it treats the previous posterior state and covariance as the prior for the next prediction–update step, so you never need to store or reprocess past measurements. You simply alternate “predict” (project forward your last estimate) and “update” (correct it with the newest measurement) as long new data arrives. In practice you run it indefinitely in real time — either for the entire duration of your sensing task or until the estimate covariance and/or Kalman gain converges to a steady‐state or falls below a defined error metric (i.e. RMSE), at which point further updates produce negligible change.

# 1D Kalman Filter


# References

[1] https://www.kalmanfilter.net/default.aspx
[2] https://medium.com/data-science/exposing-the-power-of-the-kalman-filter-1b78621c3f56
[3] https://cookierobotics.com/071/
[4] https://www.codeproject.com/Articles/865935/Object-Tracking-Kalman-Filter-with-Ease

