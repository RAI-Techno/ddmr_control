# DDMR Control - Mobile Robot Neural Controller

<p align="center">
  <img src="Images/Overall Architecture.png" alt="Demo" width="600"/>
</p>

## Table of Contents
- [Overview](#overview)
- [Dataset](#dataset)
- [System Identification Model](#system-identification-model)
- [Neural Network Controller Model](#neural-network-controller-model)
- [Authors](#authors)

## Overview
Robot control is a fundamental task in robotics, enabling a robot to follow a desired trajectory by precisely adjusting the motors' voltages. This repository focuses on a differential-drive mobile robot, where the left and right motors receive independent voltages that determine their angular velocities.

This project implements a Model Reference Neural Control (MRNC) system designed to ensure accurate tracking of the desired wheel angular velocities. The control architecture consists of two main neural components: 

1- System Identifier: a neural model that learns the robot’s dynamic behavior.

2- Controller: a neural model that learns to generate the appropriate motors' voltages so that the robot’s actual wheel angular velocities follow the reference model.

A custom **[differential-drive mobile robot](https://github.com/RAI-Techno/drl_autonomous_exploration)** was built to collect the dataset used for training these neural models.
This repository provides complete training and evaluation scripts for both the System Identifier and the Controller.


## Dataset
The dataset used in this project was gathered from a real differential-drive mobile robot during approximately one hour of movement. It contains about 33,800 samples, capturing how the robot responds dynamically to motor voltage commands.
The goal is to allow researchers, practitioners, and students to accurately model and simulate the robot’s motion by learning the mapping: motors voltage → wheels angular velocity

The dataset is stored as a CSV file with the following structure:

| Column | Description |
|:-------:|:-------:|
| Time (s) | Timestamp of each measurement (Second) |
| Left_Volt (V) | Input voltage applied to the left motor (Volt) |
| Right_Volt (V) | Input voltage applied to the right motor (Volt) |
| Left_Speed (rad/s) | Measured angular velocity of the left wheel (Rad/s) |
| Right_Speed (rad/s) | Measured angular velocity of the right wheel (Rad/s) |
<p align="center">
  <img src="Images/Control Data.png" alt="Demo" width="500"/>
</p>

## System Identification Model
The Neural System Identifier is responsible for learning the input–output behavior of a dynamic system directly from data, without requiring an explicit analytical model. For a differential-drive mobile robot, this means learning how motor voltages translate into wheel angular velocities over time.
This creates a virtual replica of the real system, enabling safe and scalable controller design and testing.

The following table presents the architecture of the proposed neural system identifier model:

| Layer (type) | Output Shape | Param # |
|:-------:|:-------:|:-------:|
| conv1d (Conv1D) | (None, 150, 32) | 224 |
| re_lu (ReLU) | (None, 150, 32) | 0 |
| conv1d_1 (Conv1D) | (None, 150, 64) | 6,208 |
| re_lu_1 (ReLU) | (None, 150, 64) | 0 |
| flatten (Flatten) | (None, 9600) | 0 |
| dense (Dense) | (None, 512) | 4,915,712 |
| re_lu_2 (ReLU) | (None, 512) | 0 |
| dense_1 (Dense) | (None, 256) | 131,328 |
| re_lu_3 (ReLU) | (None, 256) | 0 |
| dense_2 (Dense) | (None, 128) | 32,896 |
| re_lu_4 (ReLU) | (None, 128) | 0 |
| dense_3 (Dense) | (None, 64) | 8,256 |
| re_lu_5 (ReLU) | (None, 64) | 0 |
| dense_4 (Dense) | (None, 32) | 2,080 |
| re_lu_6 (ReLU) | (None, 32) | 0 |
| dense_5 (Dense) | (None, 32) | 1,056 |
| re_lu_7 (ReLU) | (None, 32) | 0 |
| dense_6 (Dense) | (None, 2) | 66 |

This model achieved an R² = 0.9936, with the following results:
<p align="center">
  <img src="Images/System Identification Loss.png" alt="Demo" width="500"/>
</p>
<p align="center">
  <img src="Images/System Identification Results.png" alt="Demo" width="600"/>
</p>

## Neural Network Controller Model
The neural controller is implemented using a Model Reference Neural Control (MRNC) scheme. Using the trained neural system identifier as a virtual plant, the controller learns to output motor voltages that drive the wheel angular velocities to follow a predefined reference model. During training, the controller receives desired wheel speeds and minimizes the error between the reference model output and the identifier’s predicted response. This setup enables safe, data-driven training without relying on the physical robot and results in a controller capable of accurately tracking wheel-speed commands in nonlinear conditions.

The following table presents the architecture of the proposed neural network controller model:

| Layer (type) | Output Shape | Param # |
|:-------:|:-------:|:-------:|
| dense_7 (Dense) | (None, 128) | 25,472 |
| re_lu_8 (ReLU) | (None, 128) | 0 |
| dense_8 (Dense) | (None, 64) | 8,256 |
| re_lu_9 (ReLU) | (None, 64) | 0 |
| dense_9 (Dense) | (None, 32) | 2,080 |
| re_lu_10 (ReLU) | (None, 32) | 0 |
| dense_10 (Dense) | (None, 16) | 528 |
| re_lu_11 (ReLU) | (None, 16) | 0 |
| dense_11 (Dense) | (None, 2) | 34 |


This model achieved an R² = 0.9944, with the following results:
<p align="center">
  <img src="Images/Neural Network Controller Loss.png" alt="Demo" width="400"/>
</p>
<p align="center">
  <img src="Images/Neural Network Controller Results.png" alt="Demo" width="600"/>
</p>

## Authors
- [Ali Hasan](https://github.com/Ali-Hasan-617)
- [Bashar Moalla](https://github.com/basharmoalla)
- [Yousef Mahfoud](https://github.com/yousef4422)
