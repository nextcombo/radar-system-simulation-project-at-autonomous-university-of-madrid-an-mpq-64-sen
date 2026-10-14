# Radar System Simulation Project at Autonomous University of Madrid: AN/MPQ-64 "Sentinel" Radar Simulation Implementation [with MATLAB Code]

Project Members: Miguel Carralero Lanchares and Francisco Orcha Kovacs. Introduction: This project is based on academic research, developing a pulse-based warning radar simulation system. The design inspiration comes from AN/MPQ-64 "Sentinel" radar. The core feature of this radar is the detection of moving targets through electromagnetic pulses, with the simulation process reproducing its basic working principle. However, it does not apply advanced technologies such as pulse compression, echo integration, or adaptive detection. Instead, it focuses on detecting five simple and distinct moving targets. Additionally, to enhance the realism of the detection scenarios, the project also deeply simulates the phenomenon of "target roll", which refers to the characteristic that the radar cross section of a target changes over time.
This system is developed based on MATLAB, and can complete the following core functions: calculate the maximum detection distance of radar, simulate receiving signals, implement target detection through comparison between signals and thresholds, and present three key information in a graphical way - real position of target, radar detection results, and A-SCOPE display interface reflecting the change of echo intensity with distance.
II. Radar System Parameters and Used Constants: Simulation of radar parameters is based on AN/MPQ-64 "Sentinel" radar - this radar is a military X-band 3D radar. The parameter specifications are primarily derived from the official system documentation (reference link: https://www.radartutorial.eu/19.kartei/04.battle/pubs/mpq_64.pdf), with some data missing being supplemented by radar parameters with similar performance.
The following table summarizes all constants and parameter values used in the simulation:
Parameters
Numbers
Unit
Notes
Peak power ( )
W  
Radar transmitting power
Antenna gain ( )
30 dB (equivalent value 1000)
-  
The antenna's ability to amplify signals
Working frequency ( )
9  
GHz  
Frequency of radar transmitting signals
Pulse width ()
1  
μs  
Duration of a single emission pulse
System loss ( )
5  
dB  
Total signal attenuation across all stages of the radar system
Noise figure
4  
dB  
Indicator of the noise level in the receiver of radar
System temperature ( )
290  
K  
Standard noise temperature (reference value in normal environment)
Target average radar cross section ()
1  
Average quantization value of target reflecting radar signal capability
False alarm probability ()
-  
The acceptable probability of radar misidentifying noise as a target
Detection probability ( )
0.9  
-  
The expected probability of radar correctly detecting a real target.
Antenna beam width
3  
Degree
Instantaneous spatial angle range of antenna coverage
3.1 Calculation of the Maximum Detection Range (Simulation's first step is to determine the theoretical maximum detection range of the radar under ideal conditions, assuming the target is a "non-tilted" object (i.e., the radar cross section remains constant, conforming to the Swerling 0 model, Swerling 0). The calculation utilizes the basic equation for a single-base radar, which takes into account antenna gain, operating frequency, signal attenuation, thermal noise, and the minimum signal-to-noise ratio threshold required to achieve the desired detection probability (...).
In this simulation, the target's average radar cross-section is taken as _, and the minimum signal-to-noise ratio is calculated using a specific function based on the preset detection probability (_) and false alarm probability (_).
Simulation results indicate that particular attention should be paid: subsequent simulations will introduce "varying radar cross-section targets" (i.e., targets with varying radar cross-section), but this feature was not considered in the calculation of maximum detection distance here. It is merely a reference value for system design. For more rigorous analysis, adjustments to the minimum signal-to-noise ratio (SNR) based on the selected Swerling model are required.
3.2 Minimum resolution and minimum detection distance radar range resolution () and minimum detection distance () are directly determined by the pulse width of the transmitted signal, where c is the speed of light.
¼  
For pulse width.
Simulation results.
Minimum Resolution: 149.90 m
•  
Minimum detection distance: 149.90 m
3.3 Pulse Repetition Frequency (PRF) and Resolution Without Clutter
Simulation results.
No ambiguity maximum distance: km
3.4 After determining the minimum signal-to-noise ratio, the receiver sensitivity and detection threshold are first calculated by the average noise power of the receiver. Subsequently, the minimum detectable power at the input of the receiver is derived from this calculation, ultimately resulting in the minimum detectable voltage after envelope demodulation. The radar detection threshold is calculated based on the Rayleigh Noise Model.
IV. Mobile Object Simulation Scene: The simulation scene includes 5 independent mobile targets, each with a unique initial position, movement direction, and velocity. The initial position of the target is randomly generated in polar coordinates, ensuring that some targets are within the radar's maximum detection range and others are outside, thereby simulating common radar phenomena such as "blind zones" and "distance ambiguity."
4.1 Initial positions are randomly generated for each target by using polar coordinates: the azimuth angle is uniformly selected within the range of 0~2π radians, and the radial distance is between two thresholds (the specific thresholds are set according to the simulation scenario). The design of this distance range includes both the radar detectable area and the areas outside the detection range, which can intuitively show phenomena such as blind zones and distance ambiguity.
4.2 After the initial position of the movement direction is set, its movement direction is set to directly towards the radar (the radar is located at the origin of the coordinate system).
4.3 Speed Design Related to Distance Ensures that the initial target has sufficient time to enter the radar's key detection area at a distance, with the initial speed of the target being dynamically adjusted based on its initial position:
•  
If the target initial position exceeds the maximum distance of no-blur (, then assign a higher speed, ranging from 600 to 900 m/s;
•  
If the target's initial position is within the radar's effective detection range, the speed shall be adjusted to 400~650 m/s.
This design ensures the dynamic nature of the simulation process, allowing the target to traverse through different radar spatial regions within the simulation time.
4.4 To accurately depict the target's movement process, the system will continuously record the position information of each target. These historical data will be used later to plot the complete trajectory of the target in the "Real Position Display Interface" and to compare it with the echo track detected by radar.
V. Realization of Variable Targets: To more accurately simulate the actual detection environment, the simulation system introduces a "variable target" model - that is, the radar cross-section (RCS) of the target changes with time. This project employs the first-order exponential distribution to simulate this variation, equivalent to the Swerling I or II model (Swerling I/II): In each pulse cycle, the instantaneous radar cross section of the target is generated through a random variable, leading to a natural fluctuation in the received backscatter power, thereby affecting the radar detection probability.
When this feature is implemented, it generates new instantaneous radar cross-section values based on the "average radar cross-section" set during the design phase for each iteration of the loop. The radar system will calculate the received power based on this instantaneous value, thus simulating the real-time changes in target radar cross section with simplicity and efficiency.
VI. Visualization of radar detection environment and results through four core display interfaces, with the following functions and layouts:
6.1 Real-Position Display Interface (Top Left) This interface presents the real physical space where the radar and moving targets are located: the radar is fixed at the origin of the coordinate system, with two concentric circles representing "Maximum Detection Distance (___)" and "No Clutter Maximum Distance (___)" respectively. The area between the two circles is marked in light yellow shading, indicating the radar blind zone — this region cannot achieve effective detection.
Through this interface, one can precisely observe the true trajectory of the target's movement, its continuous state of motion, and whether the target has entered or left the radar coverage range or the blurred area. It should be noted that the interface does not mark "Minimum Detection Distance (___)" — its value of only 149.9 m is too small compared to the maximum detection distance of 17.11 km, which cannot be clearly displayed on the current display scale.
Furthermore, since the initial position of the target is random, there may be instances where the target overlaps with the default legend on the interface. In such cases, users can manually adjust the legend position by placing the mouse over it, clicking and dragging to a suitable area.
6.2 PPI Display Interface (Right Side, Plane Position Display) This interface uses polar projection to simulate the "real-time observation perspective" of the radar environment: the antenna's azimuth scanning is represented by a dynamic line that rotates clockwise, with targets within the effective detection range marked as poles in polar coordinates.
The radial scale of the interface is measured in kilometers and can be dynamically adjusted — if a target exceeds the No Clutter Maximum Distance (___), the interface will automatically adapt the scale, but such targets may appear as "distance blurs" (i.e., the displayed distance is less than the actual distance). The interface also marks "Maximum Detection Distance (___)" and "No Clutter Maximum Distance (___)", facilitating comprehension of the radar coverage range.
The interface features a dark background and green lines for enhanced contrast between the target and the background, improving visual clarity. Additionally, each target echo is displayed on the interface for a limited duration of "1.5 scan cycles," ensuring that each target trajectory displays no more than 1~2 latest detection points, preventing overlapping trajectories.
6.3 A-SCOPE Display Interface (Center of the bottom) This interface displays the relationship between the echo amplitude and distance by using "distance (km)" as the horizontal axis and "received signal amplitude" as the vertical axis, presenting the variation of echo amplitude with distance. Each peak represents a target echo, and the red dashed line in the interface is the detection threshold (...) - echoes that exceed this threshold are considered "valid detection targets."
This interface is a core tool for analyzing each pulse echo signal, visually presenting distance-blurred targets (displayed distances far less than actual distances), and targets that "stand out" from noise due to an instantaneous increase in radar cross section. The interface updates in real time with radar scans, dynamically reflecting the corresponding relationship between received power and distance.
6.4 Integrated Display Interface This interface integrates the three independent interfaces described above into a MATLAB graphical window using a subfigure layout. Users can observe simultaneously the changes in the real trajectory of the target, the detection response on the radar PPI interface, and the details of the echo signals on the A-SCOPE interface.
This comprehensive view is crucial for verifying the system's real-time performance, helping users identify the reasons for the disappearance of blurred or blind-zone targets, and comparing the consistency between the true position of the target and the radar detection position. The dimensions and layout of the subfigures in the interface have been optimized to ensure that all key information is clearly visible while maintaining a harmonious scale of each subfigure.
After running the simulation code, the integrated interface will be automatically generated, allowing users to monitor the dynamic interaction process between the radar and the target in parallel.
Section 7 Case Studies and Detection Results Based on the core elements defined in previous sections (detection distance calculation, moving target modeling, radar cross-section fluctuations, and graphic display), this section analyzes the performance of the radar system under different detection scenarios, demonstrating its detection capabilities and inherent limitations through PPI, A-SCOPE interfaces, and trajectory recordings.
This study is based on a simulation experiment that includes 5 moving targets - all with random trajectories but heading towards the radar. The initial conditions for one of the targets (Target 4) are as follows: initial distance of 21.9 km, initial bearing angle of 208.6°, and initial velocity of 488.8 m/s.
7.1 Detection of targets within the effective range When targets meet the following criteria simultaneously, they can be effectively detected by the radar: "being within the antenna azimuth scanning beam, having a distance greater than the minimum detection distance (), having a distance less than the unambiguous maximum distance (), not being in the blind zone (not being in the range between and )," etc.
If the target echo amplitude exceeds the "absolute envelope detection threshold," it will be marked on the PPI interface with the correct bearing angle and displayed distance (modulated); at the same time, it will appear as a significant peak above the noise level on the A-SCOPE interface.
7.2 Target detection in the blind zone (between and ) When a target's physical position is in the blind zone, the system will not detect and evaluate its echo, so this target will not be displayed on the A-SCOPE or PPI interface. This design simulates the inherent limitations of actual pulsed radars - due to the "waiting for echo time" limitation, the radar cannot cover the range of the blind zone.
7.3 Targets beyond the unambiguous maximum distance For targets whose actual distance is greater than the unambiguous maximum distance (), their displayed distance will be calculated as "blurred distance," resulting in "displayed distance far less than actual distance," creating a "distance aliasing" effect - a typical manifestation of radar range aliasing (Aliasing) effect, which is reflected on the PPI interface as the target's position severely deviates from its true position.
7.4 Targets initially within the beam, if their initial positions simultaneously satisfy both conditions of "being within the antenna beam" and "being within the effective detection area", will be detected by the radar immediately. This phenomenon can be observed during simulation startup or when the target re-orients towards the radar after reset, and the stability of the target's movement direction ensures that it facilitates the analysis of the complete detection process from "remote approach" to "close proximity departure".
7.5 Passing through radar targets (...) When a target directly passes by the radar antenna, its distance will be less than the minimum detection range (...), and even if the target is within the antenna beam and the echo amplitude exceeds the threshold, it will not be considered an effective target. Therefore, the target will "temporarily disappear" on the radar interface until it moves away from the radar and its distance exceeds again before reappearing, which is represented on the PPI interface as a "brief interruption" in the target trajectory.
7.6 Reset of distant targets if the actual distance exceeds 1.5 times the "initial generation distance". The system will automatically reset the target, assigning new motion parameters including generating a new random initial position (still facing the radar), allocating a new motion speed, and clearing the target's historical trajectory records. This design ensures that there is always a stable number of active targets during the simulation process, facilitating the assessment of multiple detection scenarios in a single simulation.
7.7 Simulation Ending Conditions The simulation termination includes two triggering methods to ensure the process is controllable and resources can be properly released:
1  
Time-triggered: If the preset simulation duration () is greater than 0 and the current time exceeds this duration, the simulation will automatically terminate.
2  
Manual trigger: After the user closes the integrated display window, the simulation main loop terminates.
Both methods trigger resource release operations and output a simulation end prompt message to the user.
7.8 The detection characteristics of the A-SCOPE interface are that the echo peaks of the target in each scanning cycle will be superimposed on the noise background. If there are multiple detectable targets, the A-SCOPE interface will display multiple independent peaks at different distances, and the interface will be updated in real time with the main loop iterations, dynamically reflecting changes in the radar detection environment.
7.9 The dynamic scale adjustment system for PPI and real position interfaces adjusts the coordinate range dynamically based on the detection position and movement of the target. This ensures that even if the target moves quickly towards the edge area or new targets appear in the peripheral area, the interface can automatically adapt to the scale without requiring manual adjustment by the user, always maintaining the target visible.
7.10 Conclusion Analysis of the above cases indicates that this simulation system can accurately reproduce the core capabilities and inherent physical limitations of pulsed rotational radar, including distance ambiguity effects, blind zones not detectable, minimum detection distance limits, radar cross section fluctuations under the Swerling model, and random noise interference. All phenomena are presented within a controllable simulation environment and visually displayed through an intuitive graphical interface, providing an effective academic tool for understanding radar working principles.
8. Problems, Limitations, and Implemented Solutions During the development of the project, the team encountered several technical challenges unrelated to radar physical characteristics, mainly focusing on computational efficiency and visual presentation. The following are the issues and corresponding solutions:
8.1 Historical data accumulation issue: Real-time recording of target positions leads to an ever-expanding data structure, not only slowing down the interface rendering speed but also potentially causing memory overflow.
Solution: Set a "maximum data volume limit" (matched with a preset time step) for each target's historical trajectory, automatically deleting the oldest data when new data is written, ensuring steady data volume.
8.2 Visual misalignment between detection and real position: The PPI interface's detection records and real trajectory timestamps are independently stored, easily leading to "visual misalignment" (such as detection points lagging behind real positions).
Solution: Adjust the display duration of each detection point to "1.5 scan cycles" to ensure temporal correlation between detection points and actual positions, restoring the true visual effect.
8.3 Visual Obscuration Issues with Legends and Targets: The initial position of the target is random, potentially leading to obstruction by the legends on the real-position interface.
Solution: Implement a "manual dragging feature" for the legends, allowing users to move them to an area free from obstruction by clicking and dragging with the mouse.
8.4 Problems with Overly Condensed or Long Trajectories Display: In the initial design, complete trajectories are displayed without filtering, which can lead to issues such as overlapping trajectories and cluttered interfaces after prolonged simulation.
Solution: Only retain "recently recorded trajectory points" on the interface, removing earlier ones to achieve a more streamlined dynamic display.
8.5 Range Expansion Issues with Real-Position Interface: If the target moves towards a distance greater than the initial coordinate range set by the interface, it may "disappear" outside the field of view.
Solution: Implement a "coordinate range auto-adjustment" feature - update the upper limit of the interface coordinates in real-time based on the maximum distance of all targets, ensuring that the target remains within view without manual intervention.
These design decisions effectively ensure that the simulation system maintains efficient computational performance, stable operation, and clear visual effects even under long-term operation or scenarios with multiple targets present.

## Images

![Madrid Autonomous University Radar System Simulation Project: AN/MPQ-64 "Sentinel" Radar Simulation Implementation [with MATLAB Code]](01.jpg)
![Madrid Autonomous University Radar System Simulation Project: AN/MPQ-64 "Sentinel" Radar Simulation Implementation [with MATLAB Code]](02.jpg)
![Madrid Autonomous University Radar System Simulation Project: AN/MPQ-64 "Sentinel" Radar Simulation Implementation [with MATLAB Code]](03.jpg)
![Madrid Autonomous University Radar System Simulation Project: AN/MPQ-64 "Sentinel" Radar Simulation Implementation [with MATLAB Code]](04.jpg)
![Madrid Autonomous University Radar System Simulation Project: AN/MPQ-64 "Sentinel" Radar Simulation Implementation [with MATLAB Code]](05.jpg)
![Madrid Autonomous University Radar System Simulation Project: AN/MPQ-64 "Sentinel" Radar Simulation Implementation [with MATLAB Code]](06.jpg)
![Madrid Autonomous University Radar System Simulation Project: AN/MPQ-64 "Sentinel" Radar Simulation Implementation [with MATLAB Code]](07.jpg)
![Madrid Autonomous University Radar System Simulation Project: AN/MPQ-64 "Sentinel" Radar Simulation Implementation [with MATLAB Code]](08.jpg)
![Madrid Autonomous University Radar System Simulation Project: AN/MPQ-64 "Sentinel" Radar Simulation Implementation [with MATLAB Code]](09.jpg)

<!-- PAYMENT_BLOCK -->

## Get this dataset

- Payment link (Stripe): [Buy now](https://buy.stripe.com/3cs8yP7sY87d0vu9AB)
- Payment QR code: ![Payment QR code](pay.png)

<!-- /PAYMENT_BLOCK -->
