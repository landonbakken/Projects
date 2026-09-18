Steering wheel (done, but always improving)
- [Picture](Pictures/SteeringWheel.png)
- [Code](https://gitlab.com/wisconsinracing/steering-wheel)
- Converted from sd card to cm4 eeprom
- Fixed dials
    - Added screen
    - How drivers change launch control and traction control (for the future)
- Fixed e-car blank screen
- Organized code significantly
- Added and removed GUI depending on what was wanted
- Used ssh and vnc to modify raspberry pi
- Used CAN
- Multithreading 
- Took driver input, made it real
- Fixed CAN freezing

---

Labjack logging (done)
- [Code](https://github.com/theradest1/Labjack-scripts)
- Labjack T7 Pro
- Uses Lua
- Also learned how to use it with python
    - Ended up having no improvements and had to be linked to computer (raspi)
- 5 strain gauges
    - Very hard to measure due to the extremly small changes in resistance
    - Used to measure if suspention linkages could be cut down for weight savings
- Voltage difference

---

Engine Tooning With Dyno:
- [Code](https://gitlab.com/wisconsinracing/ECUFiles/-/tree/main/Logging/DynoPythonTools)
- Made a python script that
    - Take in logged data (mdf)
    - Parse it
    - Process it
    - Make a graph out of it
- Used pandas library for dataframes
- Get many graphs and data for tuning:
    - Cyl1 vs Cyl2 Phi difference
        - Generates a map of cyl1 phi/cyl2 phi 
        - Multiply with current cylinder phi map
        - Makes them equal
        - Allows for balanced cylinder loads (thus more power)
    - Cyl phi (average) vs target phi difference
        - Similar to previous, but for making the ecu more accurate at setting phi
        - More confident about setting values
    - Map of sample count:
        - To make sure that enough samples of each cell was ran
    - Spark advance vs torque reduction:
        - For generating a simple version of a torque model
        - Can be used for traction/launch control to get an exact torque very quickly
        - Plots the torque reduction from the max torque of linked RPM 
    - BSFC:
        - [Graph Picture](Pictures/BSFC.png)
        - Brake-specific fuel consumption
        - the fuel (grams) per kW*hr for each rpm and torque
        - Used for finding (and improving) the efficiency of the engine
    - And more less important ones
- Store past parsed/computed data
    - Allows for really quick additional data since it tracks what has already been added
    - Used pickles since they maintain python types
        - CSVs were having issues with being so large
- Generate several types of visual representations of the data
    - Heatmaps
    - Smoothed heatmap
        - contour lines
    - 3d graph
    - Smoothed and subdivided 3d graph
    - Makes it easier to see data
    - Very pretty (:
- Interpolation of data
    - While collecting data, many times you will have holes from not having enough samples, and this is especially common when doing engine load sweeps.To keep from including outliers in the data, we often have sample thresholds that only use data that has enough samples. 
    - Instead of doing it by hand, I made a program for it
    - It never overwrites data, it only fills in places where there weren't enough samples.
    - I also made it prioritize missing data that has more filled in surrounding cells to keep from having weird patterns in the graphs
- Saves results as csv for use in ECU code

---

Torque Model:
- [Code](https://gitlab.com/wisconsinracing/ECUFiles/-/tree/main/Logging/TorqueModel)
- Made python script that takes 4D data and converts it into an equation, along with ways to visualize the data and created equation
- Took data from dyno
    - [Data Points Graphed](Pictures/TorqueModelData.png)
    - Spark retard, RPM, Load (pedal %), and wanted Torque reduction
- At any point in time, you can input RPM, Load, and the percent of torque you want to reduce by, and it will give you a spark retard amount in degrees.
    - Since spark changes are the fastest way to change torque, it is extremly important to know this data for traction control
- To be able to get any point, I used polynomial linear regression. This let me get an equation that represents the datapoints, and can be used in ECU code
- The shape of the data was too advanced for a polynomial to be fit and generalized to future points
    - To fix this, I linearized the Torque Reduction and spark retard graph, making it a more simple shape
    - [Datapoints Before Linearization](Pictures/TorqueModelBefore.png)
    - [Datapoints After Linearization](Pictures/TorqueModelAfter.png)
    - This let it be generalized since it was a much more simple graph to fit to
    - Instead of doing spark retard squared, I changed it to square root of torque reduction to make it independant of the other variables.

---

New ECU CAN:
- generated CAN code using cantools and a dbc for custom 

---

Traction control (not done)
- Feedforward for launch control
- Tunable model that is time vs torque
- Stability control
- Yaw based

---

Prune of E-Car ECU code (not done):
- Reducing uneccisary code that isn't being used
- makes it more readable, and easier to add things in the future
- simplifies the output to the motors



