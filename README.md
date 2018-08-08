# Lunar Habitat Sensors
  The source for the TelosB lunar habitat sensors.
  
## Getting Started
A Raspberry Pi with Raspbian OS was used, along with Crossbow TelosB sensors.

### Prerequisites
This depends on TinyOS and Python 2.7. TinyOS relies upon the nesC compiler.

## Installation
This guide assumes a fresh installation of Raspbian.
nesC will be compiled and installed, then TinyOS will be downloaded, compiled, and installed.

### Installing nesC
First, the prerequisites for the nesC compiler will be installed.
Open a terminal and run the following:

    sudo apt-get update
    sudo apt-get install emacs gperf bison flex git autotools-dev automake
After those are installed, reboot the Pi with:

    sudo reboot -h -now
Next, the git repository for nesC will be cloned.

    git clone https://github.com/tinyos/nesc.git
Then, nesC will be compiled and installed.

    cd nesc/
    ./Bootstrap
    ./configure
    make
    sudo make install
nesC should now be successfully installed.

### Installing TinyOS
The essentials for TinyOS will be installed next. Run the following:

    sudo apt-get install build-essential openjdk-8-jdk openjdk-8-jre python2.7 python2.7-dev avarice avr-libc msp430-libc avrdude binutils-avr binutils-msp430 gcc-avr gcc-msp430 gdb-avr subversion graphviz python-docutils checkinstall
Next, the git repo for tinyOS will be cloned.

    cd ~
    git clone https://github.com/tinyos/tinyos-main.git  
Now, type

    sudo nano ~/.bashrc
And add the following lines to the end of the file

    export TOSROOT=”/home/pi/tinyos-main”
    export CLASSPATH=$CLASSPATH:$TOSROOT/tools/tinyos/java
    export PYTHONPATH=$PYTHONPATH:$TOSROOT/tools/tinyos/python
    echo "Setting up TinyOS on source path $TOSROOT"
    
Next, run

    source ~/.bashrc
    cd tinyos-main/tools
    ./Bootstrap
    ./configure
    make
    sudo make install
    
You can check if it installed correctly by running

    cd ~/tinyos-main/apps/Blink
    make telosb
If TinyOS is installed correctly, the program should compile successfully

To check if all of the necessary files are in the correct locations, plug the TelosB into one of the Pi's USB ports and run

    cd  ~/tinyos-main/tools/platforms/msp430
    make
    sudo make install
    motelist
Then use the device listed in motelist in:

    java net.tinyos.tools.Listen -comm serial@"device in motelist":telosb
For example:

    java net.tinyos.tools.Listen -comm serial@/dev/ttyUSB0:telosb
    
There may be some files missing (e.g. libtoscomm.so). Run

    sudo tos-install-jni
If that does not fix the problem, try the following
    
    cd ~/tinyos-main/tools/tinyos/jni
    sudo ln serial/libtoscomm.so /usr/lib
    sudo ln env/getenv.so /usr/lib
That should have solved the problem.

### Installing the sensor codes
Copy the "Sensors" folder into "~/tinyos-main/apps" Then, execute

    cd ~/tinyos-main/apps/Sensors
    make telosb
To install it on a TelosB device, first determine what ID you want to use, then run the following, replacing "X" with your desired ID number

    make telosb reinstall,X
Do this for all, but the base node, assigning each one a unique ID number. Next, the base node will be programmed.

    cd ~/tinyos-main/apps/BaseStation
    make telosb install
    
Finally, to run the data extraction program, execute:

    cd ~/tinyos-main/apps/Sensors
    motelist
    python TelosBRead.py serial@"device in motelist":115200
You should start to see data from the sensors appear on-screen.
