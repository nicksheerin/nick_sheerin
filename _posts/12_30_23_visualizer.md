---
title: 'Creating a Real-Time Plotting Visualizer using Python & Bokeh!'
date: 2023-12-30
permalink: /posts/12_30_23_rt_visualizer
excerpt_separator: <!--more-->
toc: true
tags:
  - bokeh
  - visualization
  - real-time
---

Guide to making a multiple plotting scripts using Python and Bokeh!

<!--more-->

I have always wanted a easy to use & basic plotting script when running doing some data analysis or visualization. The most common solution is to use the "matplotlib" python library. Although this library is fairly easy to setup and use, it lacks certain capabilities of making your visualizations look aesthetically pleasing + the added tools of easily navigating a figure. This guide serves as a way to demonstrate a way to create a visualizer where you can dynamically plot signals and understand how signals are changing in real-time. This guide will show you how to create 3 different types of visualizations: static, replaying a static file in pseudo real-time, and in real-time.

## Background

In this guide, I will be using some sample data collected from an IMU sensor. An IMU is an inertial measurement unit that measures acceleration, rotation velocities, and sometimes even magnetic fields. When an IMU measures all 3 of these quantities it is commonly referred to as a 9DOF sensor. The data from this sensor was collected while moving the sensor in space around certain axes. You can substitute this data with your own data as needed. 

## Setup

I use the Anaconda ecosystem to handle creating environments for me to do my dev work. Within this environment, I use the following libraries with their corresponding version #'s:

python 3.12.0
bokeh 3.3.0
pandas 2.1.2
numpy 1.26.1

```python
pip3 install [package_name]
```

You can use pip3 list to confirm that you have the correct packages installed. *Note I use VSCode to run my python files.

Sample data can be found downloaded [here!](https://www.dropbox.com/scl/fo/dd8rg701mbarj927ik5c6/h?rlkey=zs93q5ggh6aw6og6bepy3bggf&dl=0)

## Step-by-Step Guide on how the code is developed

#### static_visualizer.py

1. Import the packages that will be used
2. Read in the sample data using the pandas library
3. Create a function to customize each plot in the same style. This section is where you can modify fonts, labels, legends, locations, and other stylistic choices
4. Create the plots with figure dimensions, titles, line widths, and what signals will actually be displayed
5. Arrange the grids to show them in a specific order
6. Lastly, go ahead and run the python script or manually type in the terminal: 
```python
python3 static_visualizer.py
```

![](/images/posts/visualizer/static_plot.png)<!-- -->

Full code example of <static_visualizer.py>
```python

# Import the necessary libraries
import pandas as pd
from bokeh.plotting import figure, show
from bokeh.layouts import gridplot
from bokeh.palettes import Set2_3
from bokeh.models import Tabs, TabPanel

# Read in the data & adjust the time column to start at 0
df = pd.read_csv('data/imu_data.csv')
df['ref_time'] = df['ref_time'] - df['ref_time'][0]
print(df.shape)
panels = []

# create a function to make all plots have the same style
def customize_plot(fig):
    fig.title.text_font_size = "35px"
    fig.title.align = "center"
    fig.xaxis.axis_label_text_font_size = "30px"
    fig.yaxis.axis_label_text_font_size = "30px"
    fig.legend.location = 'top_right'
    fig.legend.title = "Channels"
    fig.legend.border_line_width = 6
    fig.legend.border_line_color = "black"
    fig.legend.click_policy="hide"
    fig.add_layout(fig.legend[0], 'right')
    return fig

##################### Plot the accelerometer data #########################
fig_XL = figure(title=f'Accelerometer Signals', x_axis_label='Time (s)', y_axis_label='Accel Value [g]', frame_width=1200, frame_height=300)
fig_XL.line(df['ref_time'], df['XL_x'], line_width = 3, legend_label= 'XL_x', color=Set2_3[0])
fig_XL.line(df['ref_time'], df['XL_y'], line_width = 3, legend_label= 'XL_y', color=Set2_3[1])
fig_XL.line(df['ref_time'], df['XL_z'], line_width = 3, legend_label= 'XL_z', color=Set2_3[2])
fig_XL = customize_plot(fig_XL)

##################### Plot the gyroscope data #########################
fig_GYRO = figure(title=f'Gyroscope Signals', x_axis_label='Time (s)', y_axis_label='Gyro Value [dps]', frame_width=1200, frame_height=300)
fig_GYRO.line(df['ref_time'], df['GYRO_x'], line_width = 3, legend_label= 'GYRO_x', color=Set2_3[0])
fig_GYRO.line(df['ref_time'], df['GYRO_y'], line_width = 3, legend_label= 'GYRO_y', color=Set2_3[1])
fig_GYRO.line(df['ref_time'], df['GYRO_z'], line_width = 3, legend_label= 'GYRO_z', color=Set2_3[2])
fig_GYRO = customize_plot(fig_GYRO)

##################### Plot the magnetometer data #########################
fig_MAGN = figure(title=f'Magnetometer Signals', x_axis_label='Time (s)', y_axis_label='Mag Value [uT]', frame_width=1200, frame_height=300)
fig_MAGN.line(df['ref_time'], df['MAGN_x'], line_width = 3, legend_label= 'MAGN_x', color=Set2_3[0])
fig_MAGN.line(df['ref_time'], df['MAGN_y'], line_width = 3, legend_label= 'MAGN_y', color=Set2_3[1])
fig_MAGN.line(df['ref_time'], df['MAGN_z'], line_width = 3, legend_label= 'MAGN_z', color=Set2_3[2])
fig_MAGN = customize_plot(fig_MAGN)

# Create a grid of plots to arrange the plots nicely
grid = gridplot([[fig_XL], [fig_GYRO], [fig_MAGN]], merge_tools=False)
panels.append(TabPanel(child = grid, title= f'Accel & Gyro & Mag'))

# Create a tabbed layout of the plots and display it
tabs = Tabs(tabs = panels)
show(tabs)
```

#### replay_visualizer.py

This script is very similar to the previous example with some minor changes.

1. You will need to add a specific object in Bokeh called the ColumnDataSource. You will initialize it with empty lists
2. Next, the figures will need to be defined slightly differently while also calling the ColumnDataSource object
3. You will need to make an update function that will be called periodically. Note the rollover value can be adjusted to your needs for how much data you want to retain on the plot at the same time
4. Add in the curdoc specific commands to run this in "pseudo" real-time
5. Lastly, to run this code, you will need to open up a new terminal and run the following command:

```python
bokeh serve --show replay_visualizer.py
```

Note: you will need to make sure the correct environment is activated in the terminal to ensure you have the correct packages available.

```python
# Import the necessary libraries
import pandas as pd
from bokeh.plotting import figure
from bokeh.layouts import gridplot
from bokeh.palettes import Set2_3
from bokeh.models import Tabs, TabPanel, ColumnDataSource
from bokeh.plotting import curdoc
import sys

# Read in the data & adjust the time column to start at 0
df = pd.read_csv('data/imu_data.csv')
df['ref_time'] = df['ref_time'] - df['ref_time'][0]
print(df.shape)
panels = []

# Create a ColumnDataSource object to stream data to the plots
source_data = ColumnDataSource(data={'ref_time': [], 'XL_x': [], 'XL_y': [], 'XL_z': [], 'GYRO_x': [], 'GYRO_y': [], 'GYRO_z': [], 
                                     'MAGN_x': [], 'MAGN_y': [], 'MAGN_z': []})

# create a function to make all plots have the same style
def customize_plot(fig):
    fig.title.text_font_size = "35px"
    fig.title.align = "center"
    fig.xaxis.axis_label_text_font_size = "30px"
    fig.yaxis.axis_label_text_font_size = "30px"
    fig.legend.location = 'top_right'
    fig.legend.title = "Channels"
    fig.legend.border_line_width = 6
    fig.legend.border_line_color = "black"
    fig.legend.click_policy="hide"
    fig.add_layout(fig.legend[0], 'right')
    return fig

##################### Plot the accelerometer data #########################
fig_XL = figure(title=f'Accelerometer Signals', x_axis_label='Time (s)', y_axis_label='Accel Value [g]', frame_width=1200, frame_height=300)
fig_XL.line('ref_time', 'XL_x', source=source_data, line_width = 3, legend_label= 'XL_x', color=Set2_3[0])
fig_XL.line('ref_time', 'XL_y', source=source_data, line_width = 3, legend_label= 'XL_y', color=Set2_3[1])
fig_XL.line('ref_time', 'XL_z', source=source_data, line_width = 3, legend_label= 'XL_z', color=Set2_3[2])
fig_XL = customize_plot(fig_XL)

##################### Plot the gyroscope data #########################
fig_GYRO = figure(title=f'Gyroscope Signals', x_axis_label='Time (s)', y_axis_label='Gyro Value [dps]', frame_width=1200, frame_height=300)
fig_GYRO.line('ref_time', 'GYRO_x', source=source_data, line_width = 3, legend_label= 'GYRO_x', color=Set2_3[0])
fig_GYRO.line('ref_time', 'GYRO_y', source=source_data, line_width = 3, legend_label= 'GYRO_y', color=Set2_3[1])
fig_GYRO.line('ref_time', 'GYRO_z', source=source_data, line_width = 3, legend_label= 'GYRO_z', color=Set2_3[2])
fig_GYRO = customize_plot(fig_GYRO)

##################### Plot the magnetometer data #########################
fig_MAGN = figure(title=f'Magnetometer Signals', x_axis_label='Time (s)', y_axis_label='Mag Value [uT]', frame_width=1200, frame_height=300)
fig_MAGN.line('ref_time', 'MAGN_x', source=source_data, line_width = 3, legend_label= 'MAGN_x', color=Set2_3[0])
fig_MAGN.line('ref_time', 'MAGN_y', source=source_data, line_width = 3, legend_label= 'MAGN_y', color=Set2_3[1])
fig_MAGN.line('ref_time', 'MAGN_z', source=source_data, line_width = 3, legend_label= 'MAGN_z', color=Set2_3[2])
fig_MAGN = customize_plot(fig_MAGN)

# Define an update function that will stream data to the plots when called
i=0
def update():
    global i

    source_data.stream({
        'ref_time': [df['ref_time'].iloc[i]], 'XL_x': [df['XL_x'].iloc[i]], 'XL_y': [df['XL_y'].iloc[i]], 'XL_z': [df['XL_z'].iloc[i]],
        'GYRO_x': [df['GYRO_x'].iloc[i]], 'GYRO_y': [df['GYRO_y'].iloc[i]], 'GYRO_z': [df['GYRO_z'].iloc[i]],
        'MAGN_x': [df['MAGN_x'].iloc[i]], 'MAGN_y': [df['MAGN_y'].iloc[i]], 'MAGN_z': [df['MAGN_z'].iloc[i]]
        }, rollover=1000)
    
    i += 1
    
    if i == len(df):
        print("Finished replaying the data!")
        sys.exit(0)

# Create a grid of plots to arrange the plots nicely
grid = gridplot([[fig_XL], [fig_GYRO], [fig_MAGN]], merge_tools=False)
panels.append(TabPanel(child = grid, title= f'Accel & Gyro & Mag'))

# Create a tabbed layout of the plots and display it; add a periodic callback to update the plots
tabs = Tabs(tabs = panels)
curdoc().title = 'Replay Streaming'
curdoc().add_root(tabs)
curdoc().add_periodic_callback(update, 25)
```

#### real_time_visualizer.py

This script is very similar to the previous example with difference being that data is generated in real-time (in this case emulated with some fake data). This data can be passed if coming in from a serial connection, UDP port, or some other communication protocol. The scope of connecting a sensor in real-time is out of scope of this guide. But this example provides a framework that can be modified for your needs.

```python
# Import the necessary libraries
import numpy as np
from bokeh.plotting import figure
from bokeh.layouts import gridplot
from bokeh.palettes import Set2_3
from bokeh.models import Tabs, TabPanel, ColumnDataSource
from bokeh.plotting import curdoc
import sys

panels = []

# Create a ColumnDataSource object to stream data to the plots
source_data = ColumnDataSource(data={'ref_time': [], 'XL_x': [], 'XL_y': [], 'XL_z': [], 'GYRO_x': [], 'GYRO_y': [], 'GYRO_z': [], 
                                     'MAGN_x': [], 'MAGN_y': [], 'MAGN_z': []})

# create a function to make all plots have the same style
def customize_plot(fig):
    fig.title.text_font_size = "35px"
    fig.title.align = "center"
    fig.xaxis.axis_label_text_font_size = "30px"
    fig.yaxis.axis_label_text_font_size = "30px"
    fig.legend.location = 'top_right'
    fig.legend.title = "Channels"
    fig.legend.border_line_width = 6
    fig.legend.border_line_color = "black"
    fig.legend.click_policy="hide"
    fig.add_layout(fig.legend[0], 'right')
    return fig

##################### Plot the accelerometer data #########################
fig_XL = figure(title=f'Accelerometer Signals', x_axis_label='Time (s)', y_axis_label='Accel Value [g]', frame_width=1200, frame_height=300)
fig_XL.line('ref_time', 'XL_x', source=source_data, line_width = 3, legend_label= 'XL_x', color=Set2_3[0])
fig_XL.line('ref_time', 'XL_y', source=source_data, line_width = 3, legend_label= 'XL_y', color=Set2_3[1])
fig_XL.line('ref_time', 'XL_z', source=source_data, line_width = 3, legend_label= 'XL_z', color=Set2_3[2])
fig_XL = customize_plot(fig_XL)

##################### Plot the gyroscope data #########################
fig_GYRO = figure(title=f'Gyroscope Signals', x_axis_label='Time (s)', y_axis_label='Gyro Value [dps]', frame_width=1200, frame_height=300)
fig_GYRO.line('ref_time', 'GYRO_x', source=source_data, line_width = 3, legend_label= 'GYRO_x', color=Set2_3[0])
fig_GYRO.line('ref_time', 'GYRO_y', source=source_data, line_width = 3, legend_label= 'GYRO_y', color=Set2_3[1])
fig_GYRO.line('ref_time', 'GYRO_z', source=source_data, line_width = 3, legend_label= 'GYRO_z', color=Set2_3[2])
fig_GYRO = customize_plot(fig_GYRO)

##################### Plot the magnetometer data #########################
fig_MAGN = figure(title=f'Magnetometer Signals', x_axis_label='Time (s)', y_axis_label='Mag Value [uT]', frame_width=1200, frame_height=300)
fig_MAGN.line('ref_time', 'MAGN_x', source=source_data, line_width = 3, legend_label= 'MAGN_x', color=Set2_3[0])
fig_MAGN.line('ref_time', 'MAGN_y', source=source_data, line_width = 3, legend_label= 'MAGN_y', color=Set2_3[1])
fig_MAGN.line('ref_time', 'MAGN_z', source=source_data, line_width = 3, legend_label= 'MAGN_z', color=Set2_3[2])
fig_MAGN = customize_plot(fig_MAGN)

# Generate some fake data; this can be replaced with any input data that can be appended in the update function
ref_time = np.arange(0, 100, 0.01)
XL_x = np.random.uniform(low=-100.0, high=100.0, size=(10000,))
XL_y = np.random.uniform(low=-100.0, high=100.0, size=(10000,))
XL_z = np.random.uniform(low=-100.0, high=100.0, size=(10000,))

GYRO_x = np.random.uniform(low=-500.0, high=500.0, size=(10000,))
GYRO_y = np.random.uniform(low=-500.0, high=500.0, size=(10000,))
GYRO_z = np.random.uniform(low=-500.0, high=500.0, size=(10000,))

MAGN_x = np.random.uniform(low=-50.0, high=50.0, size=(10000,))
MAGN_y = np.random.uniform(low=-50.0, high=50.0, size=(10000,))
MAGN_z = np.random.uniform(low=-50.0, high=50.0, size=(10000,))

# Define an update function that will stream data to the plots when called
i=0
def update():
    global i
    
    source_data.stream({
        'ref_time': [ref_time[i]], 'XL_x': [XL_x[i]], 'XL_y': [XL_y[i]], 'XL_z': [XL_z[i]],
        'GYRO_x': [GYRO_x[i]], 'GYRO_y': [GYRO_y[i]], 'GYRO_z': [GYRO_z[i]],
        'MAGN_x': [MAGN_x[i]], 'MAGN_y': [MAGN_y[i]], 'MAGN_z': [MAGN_z[i]]
        }, rollover=1000)
    
    i += 1
    
    if i == len(ref_time):
        print("Finished replaying the data!")
        sys.exit(0)
        
# Create a grid of plots to arrange the plots nicely
grid = gridplot([[fig_XL], [fig_GYRO], [fig_MAGN]], merge_tools=False)
panels.append(TabPanel(child = grid, title= f'Accel & Gyro & Mag'))

# Create a tabbed layout of the plots and display it; add a periodic callback to update the plots
tabs = Tabs(tabs = panels)
curdoc().title = 'Real-Time Streaming'
curdoc().add_root(tabs)
curdoc().add_periodic_callback(update, 25)
```

## Resources

https://docs.bokeh.org/en/latest/

[Sample Data & Python Scripts](https://www.dropbox.com/scl/fo/dd8rg701mbarj927ik5c6/h?rlkey=zs93q5ggh6aw6og6bepy3bggf&dl=0)

## Contact Me

Feel free to send me an email if you have any questions or comments: kbhakta96@gmail.com