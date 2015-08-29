---
layout: project
title: Syntouch BioTac Calibration and Closed-Loop Characterization
date: August 28, 2015
image: biotac.jpg
---

## Overview

The [BioTac by SynTouch](http://www.syntouchllc.com/Products/BioTac/) is a biomemetic fingertip sensor that provides the ability to sense temperature, pressure, vibration, and point of contact similar to a human fingertip.  The sensor consists of an elastomeric skin with a fluid-filled interior and an epoxy resin inner core.  Embedded within the core are 19 sensing electrodes, four excitation electrodes, a thermistor, and a pressure sensor.  

Internal structure                                                                                                    | Sensor layout
----------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------
<img src="http://mattmongeon.github.io/portfolio/public/images/biotac_diagram.png" alt="alt text" style="width:400px">|<img src="http://mattmongeon.github.io/portfolio/public/images/sensor_layout.png" alt="alt text" style="width:400px">

The fluid within the skin is a conductive solution of sodium bromide.  The sensing electrodes are used to measure its impedance, the thermistor is used to measure its temperature, and the pressure sensor measures the fluid's current pressure and detects vibrations.  This information used together can be used to determine force and point of contact along the skin.

This project has two purposes.  The first is to formulate a calibration scheme for the BioTac's sensors that can be used to convert its data output into a force in engineering units with particular attention paid to accounting for signal drift with temperature.  The second is to characterize the system response of a closed-loop control system that is operating on the BioTac.

## Force Calibration

As previously mentioned, the skin of the BioTac is filled with a solution of sodium bromide.  The sensing electrodes embedded within the core sense the fluid's impedance by receiving a quick voltage pulse from the excitation electrodes and measuring the received voltage, which can be used to determine the impedance of the path through the fluid.  This impedance changes as pressure is applied to the skin through contact, and it also changes with temperature.  The change in impedance has already been used to determine a force vector and point of contact by Lin et al [1].  Lin's method does so by removing the sensor from contact and taring the electrode values to provide a zero point from which to measure the change in electrodes as contact is made with the skin.  The changes in the electrodes are then used in the following equation to produce the force vector.

<img src="http://mattmongeon.github.io/portfolio/public/images/force_vector_eqn.png" alt="alt text" style="width:500px">.

The S terms in the equation are scalars that are determined experimentally.  The n values are normal vector components that correspond to each electrode.  Although this method does generate a force vector, it does not attempt to continuously compensate for signal drift due to the current temperature.  Instead it handles drift by periodically taring the electrodes to re-establish the zero points.  This project's aim was to characterize the signal drift with temperature and be able to continuously compensate for it in order to eliminate the need to periodically tare the electrodes.

### Electrode Drift with Temperature

As the BioTac is operating, its internal electronics are generating heat, causing all parts of the BioTac to heat up.  As the fluid temperature changes, its impedance also changes and causes the electrode values to drift as shown in this plot.

<img src="http://mattmongeon.github.io/portfolio/public/images/e_vs_tdc_thermistor.png" alt="Electrodes vs TDC" style="width:400px">

The x-axis represents the raw thermistor measurement, TDC, which corresponds to the current sensed temperature.  The y-axis represents the raw electrode values.  Each of these values arrives from the BioTac as a 12-bit value ranging from 0 to 4096.  The temperature values shown range from 25.7 to 35.3 degrees Celsius.

### Compensation of Drift

To compensate for this drift I tried using a non-linear least squares solving library, [Ceres](ceres-solver.org), to fit an exponential curve of the form 

<img src="http://mattmongeon.github.io/portfolio/public/images/electrode_eqn.png" alt="Electrode Compensation Equation" style="width:300px">

where ei is an expected electrode value, TDC is the current TDC value from the BioTac, and the remaining terms (ai, bi, ci, and di) are all determined using the solver.

When I tried to fit the curves to the data, I found that it usually did not completely offset the electrodes.  When I plotted the error between the expected electrode value and the actual recorded electrod value, I found that the fit was usually incorrect, sometimes by a significant amount.  This even occurred at times when the temperature as measured by the thermistor is very similar as shown in these plots.  These plots were generated by using BioTac data that was recorded while the BioTac was not in contact with anything to calculate an expected electrode value from TDC and compare it to the recorded electrode values to generate an error plot.

Sample Errors 1                                                                                                           | Sample Errors 2
--------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------
<img src="http://mattmongeon.github.io/portfolio/public/images/electrode_errors_1.png" alt="alt text" style="width:600px">|<img src="http://mattmongeon.github.io/portfolio/public/images/electrode_errors_2.png" alt="alt text" style="width:600px">

Notice that while some of the electrodes are at roughly the same value between the two plots, others are significantly different.  This means that the temperature compensation model above is not sufficient to reliably calculate an accurate electrode value from temperature.

### Force Calibration Results

In an effort to see how the final force values work out given the electrode errors, I recorded some tests that used the brushed DC motor to press the BioTac against a 1 kg load cell and recorded the BioTac data and the load cell reading.  I performed a large number of test runs over forces ranging from 0 N to 6 N and recorded all of their data to file.  I then used my temperature compensation curves to offset the electrode values and used Ceres to solve for the S terms in the force equation above.  Once these terms were solved, I used the calibration scheme to plot the function results with the target force for comparison.  The following plots show some example outcomes.

Results of 0N Test Runs (No Contact with BioTac)                                                                  | Results of 2N Test Runs                                                                                          | Results of 3N Test Runs
------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------
<img src="http://mattmongeon.github.io/portfolio/public/images/0N_force_1.png" alt="alt text" style="width:350px">|<img src="http://mattmongeon.github.io/portfolio/public/images/2N_force_1.png" alt="alt text" style="width:350px">|<img src="http://mattmongeon.github.io/portfolio/public/images/3N_force_1.png" alt="alt text" style="width:350px">
<img src="http://mattmongeon.github.io/portfolio/public/images/0N_force_2.png" alt="alt text" style="width:350px">|<img src="http://mattmongeon.github.io/portfolio/public/images/2N_force_2.png" alt="alt text" style="width:350px">|<img src="http://mattmongeon.github.io/portfolio/public/images/3N_force_2.png" alt="alt text" style="width:350px">

As can be seen, sometimes the results are quite accurate, but at other times the calculated value can differ by quite a bit.

### Conclusions

Modeling the temperature of the BioTac is much more complicated than being able to fit a curve as was being attempted.  The main problem with fitting the data is the presence of temperature gradients within the fluid.  There is only a single thermistor within the BioTac, so it is only able to sample the fluid temperature in its own vicinity.  The following plots give an indication that gradients do exist within the fluid.

Comparison of Electrodes 7, 8, and 16                                                                                     | Comparison of Electrode 14, 17, and 18
--------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------
<img src="http://mattmongeon.github.io/portfolio/public/images/e7_vs_e8_e7_vs_e16.png" alt="alt text" style="width:400px">|<img src="http://mattmongeon.github.io/portfolio/public/images/e17_vs_e18_e17_vs_e14.png" alt="alt text" style="width:400px">

These plots were generated from the same data set that was used to create the plot above that shows how the electrodes change with TDC.  The lines in these plots represent electrodes being compared against each other.  As the fluid heats up, the electrodes increase in value as noted above.  If the temperature of the fluid is the same throughout, the expectation is that all of the electrodes would change at the same rate or nearly the same rate.  So if the electrodes are plotted against each other, they should exhibit a linear relationship as their values change.  For these plots, the pairs of electrodes were chosen for spatial reasons.  In the left-hand plot, electrodes 7 and 8 are located next to each other, while 7 and 16 are farther apart.  And in the right-hand plot, electrodes 17 and 18 are close to each other while 17 and 14 are farther apart.  As can be seen, the pairs 7/8 and 17/18 heat up at the same rate and show a linear relationship, whereas the other two pairs show a nonlinear relationship, which indicates the presence of local temperature gradients.

Additionally, the fluid, skin, and core each have different thermal properties and will heat at different rates, which further exacerbates the problem.  And the overall problem is made particularly worse when the BioTac is contacting an object of different temperature.  In summary, the temperature model of the BioTac is too complicated to be accounted by a single thermistor.  While it may be useful for detecting contact with hot or cold objects or getting an idea of the temperature of the BioTac system, it cannot be used for a precision operation such as this one.

## References

[1] C. H. Lin, J. A. Fishel and G. E. Loeb, "Extracting Point of Contact, Force and Torque in a Biomimetic Tactile Sensor with Deformable Skin", SynTouch LLC, 2013

