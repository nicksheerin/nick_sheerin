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

- [Table of Contents](#table-of-contents)
- [Overall Summary](#overall-summary)
- [Basic Terminology \& Equations](#basic-terminology--equations)
  - [Quick Recap](#quick-recap)
- [1D Kalman Filter](#1d-kalman-filter)
- [2D Kalman Filter](#2d-kalman-filter)
- [3D Kalman Filter](#3d-kalman-filter)
- [Conclusion](#conclusion)
- [Contact Me](#contact-me)
- [References](#references)

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

The first equation we will look at is called the <u>State Extrapolation Equation</u> (otherwise known as Dynamic Model or State Space Model). This equation is the basis of the filter since it defines the dynamic model of the system / environment.

In this particular example, the dynamic model is "constant". The weight of the robot is not changing as we try to measure the weight through multiple iterations. Mathematically, this relationship can be defined as:

$$ 
\hat{x}_{n+1,n} = \hat{x}_{n,n}
$$

where \( \hat{x}_{n+1,n} \) is predicted estimate and {% raw %}$\hat{x}_{n,n}${% endraw %} is the current estimate. 

Since the Kalman filter treats the estimate as a random variable (i.e. represents the hidden state of a system that is being estimated, and its values are uncertain and influenced by random noise but follow Gaussian properties), we must also [extrapolate](https://en.wikipedia.org/wiki/Extrapolation) the "estimation variance/uncertainty". Hence the second equation is called the <u>Covariance Extrapolation Equation</u>. In real-world applications there are uncertainties in the system dynamic model. Example, the sensor used to measure the weight of an object can be influenced by temperature. Hence, the uncertainty of the dynamic model is called the **process noise ($q_n$)**. Mathematically, the relationship is defined as:

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

Let's now try an example of implementing what we learned into a practical example. In this example, we will use a **sine wave** to simulate data and will create a dynamically adjustable Kalman filter that can be tuned via some slider buttons.

Here is the sample code:

```python

import numpy as np
from bokeh.io import curdoc
from bokeh.models import ColumnDataSource, Slider, Button, Div
from bokeh.layouts import column, row
from bokeh.plotting import figure

# —————————————————————————————————————
# One-step Kalman update function, now returning all intermediates
def kalman_step(measurement, prior_estimate, prior_variance, process_noise, measurement_variance):
    # 1) Kalman gain
    K = prior_variance / (prior_variance + measurement_variance)
    # 2) State update
    estimate = prior_estimate + K * (measurement - prior_estimate)
    # 3) Covariance update
    post_variance = (1 - K) * prior_variance
    # 4) Predict next-prior
    prior_estimate_next = estimate # state extrapolation
    prior_variance_next = post_variance + process_noise # covariance extrapolation
    
    return {
        'estimate': estimate,
        'K': K,
        'post_variance': post_variance,
        'next_prior_estimate': prior_estimate_next,
        'next_prior_variance': prior_variance_next
    }

# —————————————————————————————————————
# Initial filter state
prior_est = 0.0
prior_P   = 0.1

# Streaming window and timing
N   = 500       # keep last N points
dt  = 0.01      # seconds per step
t0  = 0.0       # time counter

# Bokeh data source with a rolling window
source = ColumnDataSource(data=dict(x = list(range(N)), measurement = [0.0]*N, estimate = [0.0]*N))

# Figure setup
p = figure(title="RT 1D Kalman-Filter Stream", x_axis_label='Sample index', y_axis_label='Value', width=1000, height=600)
p.line('x', 'measurement', source=source, legend_label="Noisy measurement", line_color="red", alpha=0.6, line_width=3)
p.line('x', 'estimate', source=source, legend_label="Filter estimate",  line_color="green", line_width=3)
p.legend.location = "top_left"

# —————————————————————————————————————
# Sliders
slider_q = Slider(start=1e-8, end=1e-1, value=1e-3, step=1e-7, format="0.0000000", title="Process noise (qₙ)")
slider_r = Slider(start=1e-3, end=1.0, value=0.1, step=1e-2, title="Measurement noise (rₙ)")

# —————————————————————————————————————
# Divs to show computed values
div_K         = Div(text="K: —",               width=200)
div_post_var  = Div(text="Post-variance: —",   width=200)
div_prior_est = Div(text="Next prior est: —",  width=200)
div_prior_var = Div(text="Next prior var: —",  width=200)

stats_panel = column(div_K, div_post_var, div_prior_est, div_prior_var, width=220)

# —————————————————————————————————————
# Preset buttons
button_low_low  = Button(label="Low Process noise (qₙ) & Low Measurement noise (rₙ)", width=150)
button_high_low = Button(label="High Process noise (qₙ) & Low Measurement noise (rₙ)", width=150)
button_low_high = Button(label="Low Process noise (qₙ) & High Measurement noise (rₙ)", width=150)
button_high_high = Button(label="High Process noise (qₙ) & High Measurement noise (rₙ)", width=150)
button_reset    = Button(label="Reset", width=150)

def set_preset(q_val, r_val):
    slider_q.value = q_val
    slider_r.value = r_val

button_low_low.on_click(lambda: set_preset(1e-6, 0.01))
button_high_low.on_click(lambda: set_preset(0.1, 0.01))
button_low_high.on_click(lambda: set_preset(1e-6, 0.8))
button_high_high.on_click(lambda: set_preset(0.1, 0.8))
button_reset.on_click(lambda: set_preset(1e-4, 0.1))
button_panel = column(button_low_low, button_high_low, button_low_high, button_high_high, button_reset)

# —————————————————————————————————————
# Streaming callback
def stream_step():
    global prior_est, prior_P, t0

    # 1) Read sliders
    q_val = slider_q.value
    r_val = slider_r.value

    # 2) Generate synthetic sinusoidal measurements
    true_val = np.sin(2 * np.pi * 0.3 * t0)
    meas = true_val + 0.5 * np.random.randn()

    # 3) Kalman update with all parameters
    result = kalman_step(measurement=meas, prior_estimate=prior_est, prior_variance=prior_P, process_noise=q_val, measurement_variance=r_val)

    # Unpack results
    est                 = result['estimate']
    K                   = result['K']
    post_variance       = result['post_variance']
    prior_est, prior_P  = result['next_prior_estimate'], result['next_prior_variance']

    # 4) Stream new point, keep rolling window
    new_x = source.data['x'][-1] + 1
    source.stream({'x':[new_x], 'measurement': [meas], 'estimate': [est],}, rollover=N)

    # 5) Update Divs
    div_K.text         = f"K: {K:.4f}"
    div_post_var.text  = f"Post-variance: {post_variance:.4f}"
    div_prior_est.text = f"Next prior est: {prior_est:.4f}"
    div_prior_var.text = f"Next prior var: {prior_P:.4f}"

    # 6) Advance time
    t0 += dt

# Register callback every 10 ms
curdoc().add_periodic_callback(stream_step, 10)

# Layout
controls = row(slider_q, slider_r, stats_panel, button_panel)
curdoc().add_root(column(controls, p))
curdoc().title = "RT 1D Kalman Filter Explorer"
```

To run this code in terminal:
```python
bokeh serve --show <name_of_file>.py (in my case I named it "interactive_1D_KF.py")
```

<div align="center">
  <img src="../../images/projects/kalman_filter/1D_example.gif" width="90%">
</div>

The Kalman filter is defined as a function that will take in the necessary inputs and compute the corresponding variables defined in first section. Specifically, as you run this example, you will notice that under different conditions of choosing process noise and measurement noise you will see the Kalman filter adjust according to what it trusts more -- either the new measurement or previous estimate. In the case of the high process noise and low measurement noise, the Kalman Gain is close to 1, indicating that the Kalman Filter does not highly weight the new incoming measurement. However, when the high process noise and high measurement noise is employed, there is a finer balance and one can see that the filter is adapting and adjusting to changes more accurately. **Note, that is heavily dependent on the context of what you are trying to filter, so it is crucial to try to understand how these values can affect your performance when using this algorithm.** 



# 2D Kalman Filter

Work in Progress

# 3D Kalman Filter

Work in Progress

# Conclusion

Work in Progress

# Contact Me
Feel free to send me an email if you have any questions or comments: kbhakta96@gmail.com

# References

[1] https://www.kalmanfilter.net/default.aspx

[2] https://medium.com/data-science/exposing-the-power-of-the-kalman-filter-1b78621c3f56

[3] https://cookierobotics.com/071/

[4] https://www.codeproject.com/Articles/865935/Object-Tracking-Kalman-Filter-with-Ease

