# Installing
## Add the submodule

From the root of the project (e.g. path/to/projects/skystone) run git submodule add https://github.com/acmerobotics/robomatic.git

In the future after cloning the project new users will need to run git submodule init and git submodule update to get the submodule.
Installation

In the build.release.gradle file in both the FtcRobotController and TeamCode modules, add the line implementation project(':Robomatic') to the end of the dependencies. Sync gradle when prompted.

Follow the instructions to install the dashboard. In step three make sure to add the dependency to the Robomatic module. During step 8, in the same locations you are told to add lines, add 

OpModeConfigurationActivity.populateMenu(menu, this);
in the first location and 

OpModeConfigurationActivity.populateMenu(popupMenu.getMenu(), FtcRobotControllerActivity.this);

in the second.
Usage
Robots

The robot class controls the main loop that provides efficient asynchronous control of hardware. Hardware devices and CachingSensors accessed through the robot will only communicate with the rev hubs once per loop, and only write data if they need to. Each iteration of the main loop the robot will: update bulk data from the rev hubs, update caching sensors, call each of the subsystem's update method, update dashboard telemetry, and finaly write any changes to motor or servo commands to the rev hubs.
Usage

To make your own robot, extend the Robot class. The robot will contain all the subsystems and interface with all the hardware.

Enable devices connected to a rev hub by registering the hub with the robot by puting the line registerHub("<configured name>") in the constructor of your robot class.

After initializing the subsystem in your robot's constructor register the subsystem with registerSubsystem(<subsystem>);

Pass the robot into your subsystems so they can interact with the robot loop and hardware.
Accessing Hardware

To use the caching hardware devices provided by the robot, access hardware from the robot rather than directly from the hardware map. The methods getMotor, getServo, getDigitalChannel, and getAnalogSensor take a name a device is registered with in the hardware map, and return a hardware device that is managed by the robot.

To memoize your own expensive sensor reads, for example reading an imu's orientation or other i2c devices, wrap the expensive call in a CachingSensor, which takes the call as its sole arguement, and then register the sensor with the robot using robot.registerCachingSensor. The lambda will be evaluated each loop, and the memoized return value of the function is accesable through the CachingSensor's getValue method. To improve loop times, disable the sensor when not in use by calling setEnabled(false). The sensor can be re-enabled when it is needed again.

To create an imu, call the robot's getRevHubImu method, and pass the index of the hub to get it from (this refers to the order in which the hubs were registered with the robot, not the addresses of the hubs). To preform an optional axis remap pass a Robot.Orientation as the second arguement. This orientation defines the directions of the positive x, y, and z axes in the imu's origional refrence frame. Refer to Bosch's documentation for details.
Updating the robot

Robot.update() will preform one update of the robot. If you have your own main loop, such as a loop in teleop that updates gamepad controls and such, call this once each loop.

The robot can also run its own main loop, updating its self until one of several conditions are met.

runUntilStop will run the main loop until the opmode is stopped.

runForTime will run the main loop for a specified number of milliseconds. This is useful if you want a delay between actions in auto, but would like the loop to continue to run.

runUntil accepts a lambda target. The loop will be run until the lambda returns true.
