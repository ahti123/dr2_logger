# Dirt Rally 2.0 Logger #

Dirt Rally 2.0 Logger is a logging and analysis tool for car setups in Dirt Rally 2.0 by Codemasters. 
This is a free and open-source tool written by Philipp Erler.

- [Executable (Version 1.7, 2020-03-25)](https://philipperler.net/dr2logger_1_7/)
- [Source Code, Documentation and Support](https://github.com/ErlerPhilipp/dr2_logger)

## Older Releases ##

- [Executable (Version 1.6, 2019-10-14)](https://philipperler.net/dr2logger_1_6/)
- [Executable (Version 1.5, 2019-10-01)](https://philipperler.net/dr2logger_1_5/)
- [Executable (Version 1.4, 2019-09-14)](https://philipperler.net/dr2logger_1_4/)
- [Executable (Version 1.3, 2019-09-02)](https://philipperler.net/dr2logger_1_3/)
- [Executable (Version 1.2, 2019-08-25)](https://philipperler.net/dr2logger_1_2/)
- [Executable (Version 1.1, 2019-08-19)](https://philipperler.net/dr2logger_1_1/)
- [Executable (Version 1.0, 2019-08-17)](https://philipperler.net/dr2logger_1_0/)

## How to Use ##

1. Enable UDP data for Dirt Rally 2.0
    1. Open the hardware_settings_config.xml (by default in "C:\Users\ [username] \Documents\My Games\DiRT Rally 2.0\hardwaresettings\hardware_settings_config.xml")
    1. Set udp enabled="true"
    1. Set extra_data=3 to get all information
    1. Set ip="127.0.0.1" to keep the information on localhost
    1. Set port=20777 (you would need to change the port in the dr2logger every time otherwise)
    1. Set delay="1" so that DR2 ends the current car state at 100 FPS (important for derived values)
    1. You can add the 'custom_udp' line multiple times for multiple telemetry tools.
    1. Example:
        ```xml
        <motion_platform>
            <dbox enabled="false" />
            <udp enabled="True" extradata="3" ip="127.0.0.1" port="20777" delay="1" />
            <custom_udp enabled="false" filename="packet_data.xml" ip="127.0.0.1" port="20777" delay="1" />
            <custom_udp enabled="false" filename="packet_data.xml" ip="127.0.0.1" port="10001" delay="1" />
            <fanatec enabled="false" pedalVibrationScale="1.0" wheelVibrationScale="1.0" ledTrueForGearsFalseForSpeed="true" />
        </motion_platform>
        ```
1. Download and unzip dr2logger.zip archive
1. Run the dr2logger.exe while you play DR2
1. After each race, the logger will save the current data
1. At the start of a race, the logger will delete the old data
1. Switch (Alt+Tab) from DR2 to the logger to create the plots
1. Remarks:
    1. You can edit the settings.ini to change the path for automatic session saves, the ip and port.
    1. Don't save, load or analyze your run while the race is running. Otherwise, data might get lost. Pausing the race is sufficient.
    1. This tool will probably work with other racing games by Codemaster, for example Dirt Rally 1 and Dirt 4. Those games use the same datastructure for the UDP packages. However, I didn't test it. Other racing games with UDP output, such as Project Cars, will require trivial changes in this tool's networking code.

![dr2logger](img/dr2logger.png)
You should see this amazing command-line window.

## How to Evaluate ##

Let's see how to use this tool to analyze your car setups. This is an example with the Mitsubishi Lancer Evo VI on a Polish track. We can analyze how the car reacts to the high speeds, mixed road and irregular surface. I'll explain what kind of information you can pull from the plots and how you can improve certain aspects of the setup.

### Track Information ###

![Track info](img/track.png)

This plot gives some basic information about the track such as elevation, route and used gear.

### Gears ###

![RPM](img/rpm.png)

This plot shows how much time you spent in which gear at which RPM. I spent most time in gear 4 and 5 while the other gears are hardly used. The lower three gears should be set much longer while the higher three gears should be somewhat shorter.

![RPM](img/speed_rpm.png)

This plot shows the speed at varying RPM. The blue series of points in the lower right corner is the acceleration from the start line. The traction is too low, therefore the wheels are spinning without really pushing the car. The slope of the different point groups varies with the gears. This might be wanted but can also be a hint that the gear ranges are off. The RPM histograms back the hint in this case.

### Wheel Speeds and Differential ###

![Drift](img/drift.png)

In this plot, you can see the forward direction of the car and its movement direction. The more these vectors disagree, the more your car is drifting. The histogram in the middle shows how often you drifted how much while the right histogram shows how fast the drift angle changed. The Evo VI is a rather easy to control car. The histograms tell that the drift angle was mostly below 20 degree. The angular velocity was also mostly below 20 degrees per second and therefore quite controlled. This supports the claim that the car can be controlled with the current setup. 

![Wheel Speed](img/wheel_speed.png)

This plot shows the wheel speeds. While the topmost graph is about individual wheels, the middle plot compares sides of the car. As an example, one line is the average of the left wheels minus the average of the right wheels. The graph at the bottom shows the inputs, i.e. throttle, brakes and steering. Make sure to use the magnifying glass to zoom into the plots. This plot is rather hard to interpret but you should still be able to find hints of bad setups. The four wheels have approximately the same speed most of the time. Only strong steering accompanied with throttle or brakes make a significant difference. So, the setup is probably ok. This is again a hint that the Evo VI is rather easy to handle. 

Let's analyze the 'bubble' in the middle of the upper graph in more detail. Here, the front-left and rear-right wheels were considerably faster than the other wheels. It seems that the differential took action here. While one wheel of each axis accelerated, the other wheel decelerated. Probably, the surface was bumpy here. The decelerated wheels are unfortunately the ones that have contact to the road. Therefore, you might want to set the differential stronger to be faster in such situations. Also, there is a sudden change in steering direction together with sudden full throttle. This weight transfer combined with bumps is not handled nicely by the suspension. The next two plots give more insight into such issues.

### Suspension ###

![Suspension](img/suspension.png)

This plot shows the suspension dislocation of individual wheels and compares sides of the car. A negative dislocation means that the spring is compressed. Quite often, the springs were fully compressed at -100 mm. Since there is no information transmitted from DR2, we can only assume that the bump stops were hit at this dislocation. Hitting the bump stops is very bad for the stability and can cause crashes. If you see such a behavior, you should definitely increase the ride height. If it's already at the limit, you can still stiffen the springs and damping. Better a harder suspension than hitting the bump stops.

The lower graph displays the averaged front/back and left/right statistics. You can see that the rear springs are more often fully compressed. Therefore, you should probably stiffen the rear springs or dampers.

![Rotation vs Suspension](img/rot_vs_susp.png)

This last plot (for now) compares spring dislocation with car roll and pitch. With a good suspension, the car should be almost unaffected by surface irregularities. However, when the terrain is too rough, e.g. with camber, the car must tilt to the side. Similarly, driving over bumps shouldn't throw the car around but slopes and jumps of course rotate the car around its pitch axis.

The suspension angles are approximated for the width and length of the Audi Quattro S1. So, the absolute values of the suspension rotation graph will be off for other cars. But we care mostly for the relation between both graphs, anyway.

In the upper graph with the car rotations, we can see that the lines have much lower frequencies than the middle graph with the suspension angles, as it should be. Looking at the center of the car rotation graph (upper graph),we see that the car rotates quickly backwards because the springs (middle graph) could not compensate the pressure anymore since they were already fully compressed (lower graph). This was probably the landing after a longer jump.

## Raw Data ##

I can only use the information I get from Dirt Rally 2.0 via UDP. This is Currently:

1. run time (starts after loading screen)
1. lap time (starts after countdown)
1. distance (driven distance)
1. progress (0.0..1.0)
1. car position (3D vector in world space)
1. forward speed (m/s as shown in HUD)
1. car velocity (3D vector in world space)
1. roll (3D vector in world space)
1. pitch (3D vector in world space)
1. suspension dislocation per wheel (mm)
1. suspension dislocation change per wheel (mm/s)
1. wheel speed per wheel (m/s)
1. throttle (0.0..1.0)
1. steering (-1.0..+1.0)
1. brakes (0.0..1.0)
1. clutch (0.0..1.0)
1. gear (-1,0,1..n)
1. g-force (lat-lon)
1. current lap
1. RPM of engine
1. Brake temperature
1. Laps
1. Track length
1. Max / idle RPM
1. Max gears

See [networking.py](./networking.py) for more information.

## How to Build ##

1. Download the code and unzip or pull the repository
1. If you don't have it already, install a Python 3, e.g. Anaconda with Python 3.7
1. If using Anaconda, you should create a new environment for this project
1. Use conda or pip to install the required packages:
    1. numpy
    1. scipy
    1. tk
    1. basemap
1. Build and run "python dr2logger.py"
1. To build an executable, update the paths in "dr2logger.spec" and run "pyinstaller dr2logger.spec"

## Open Issues and Contributing ##

Please, feel free to send pull requests when you have improvements for the code and documentation.

So far, I had no idea how to visualize and analyze cornering. Should the car change its direction faster? How much speed is lost in the process? How does e.g. toe and camber angle affect the acceleration and speed on straights? I think, you probably have a good feeling for the required steering and the overall performance can be measured simply with the race time. In contrast, you can hardly have a feeling for optimal gear and suspension setting. So, I focused more on these things. Anyway, if you have suggestions for missing plots, for improving plots and for more derived information, please tell me (in the project forum).

On the long run, comparing two or more recordings would be great. However, this is not trivial because most plots would need a common registration. Using the run time or progress won't work. Using the progress could work but would make time-dependent data hard to understand or even useless.

You may see 'unknown car' or 'unknown track' in your logs. This happens when the internal database is outdated. In this case, please fill in the car names in the 'unknown cars.txt' and 'unknown tracks.txt' that you should find in the dr2logger directory. Then please send the contents of those files to me. I didn't yet buy season 3 and 4, therefore those tracks and cars are unknown.
Due to ambiguous data (exactly the same idle/max RPM and number of gears), the following cars cannot be distinguished:
- Mitsubishi Lancer Evo VI and BMW M2 Competition
- Mitsubishi Lancer Evo X and Peugeot 208 T16
- Lancia Delta S4 RX, non-RX and Peugeot 208 R2
- Peugeot 205 T16 RX and non-RX
- Skoda Fabia Rally and Subaru WRX STI RX
- Renault Megane R.S. RX (2019) and Subaru Impreza WRX STI NR4
- Peugeot 208 WRX Supercars (2019) and Renault Clio R.S. RX
- Audi S1 EKS RX Quattro Supercars (2019) and Volkswagen Polo R Supercar
- Ford Fiesta RXS Evo 5 and Ford Fiesta RX (Stard)

## License ##

All parts of this project are published under the permissive [MIT license](./LICENSE). In short, you can do with the code, documentation, images and so on whatever you want. But don't complain if it doesn't work as expected.

If you share (parts of) the dirt rally 2 logger, I'd be happy if you mention or link my work. If you find this tool useful, please drop me a message. This would make me feel good :)

## Change Log ##

- 1.7 (2020-03-25):
    - Added cars and tracks for the Flat Out Pack
    - Added (experimental) ground-contact detection
    - Added (experimental) energy and power plots
    - Simplified game-state detection
- 1.6 (2019-10-14):
    - Added automatic car and track detection.
    - Improved automatic race detection.
- 1.5 (2019-10-01): 
    - Improved the automatic detection of races.
    - Input and output IPs and ports can now be changed in the 'settings.ini'.
    - You can now change the path for automatic session saves.
    - The logger can now forward received UDP packages to another socket. This way, you can use the logger and telemetry tools at the same time.
- 1.4 (2019-09-07): 
    - Added automatic detection and saving of runs.
    - Prevent a crash caused by not enough data for plots.
- 1.3 (2019-09-02): 
    - Keyboard I/O is now in a separate thread.
    - The current state is now printed in the window title.
- 1.2 (2019-08-25): 
    - Improved histograms.
    - Added error handling for sockets.
- 1.1 (2019-08-19): Improved plots for suspension and rot vs susp.
- 1.0 (2019-08-17): Initial release.

## TODO List ##

- put socket I/O in extra thread
- analyze slip
- Fourier transform on suspension to check dampers?

## Related Tools ##

Aside this tool, which focuses on the analysis and visualization of single runs, there are other tools.

[Dirt Rally Time Recorder](https://github.com/soong-construction/dirt-rally-time-recorder):
This tool allows you to track your stage times in DiRT Rally and browse them, which the game itself does not allow.

[RaceTrack Data Acquisition](https://www.racedepartment.com/downloads/racetrack-data-acquisition-rda.26829/):
RDA is an application that can log telemetry from DiRT Rally and DiRT Rally 2.0 to CSV, BIN, or MoTeC LD files.
It includes an overlay window with delta times.

