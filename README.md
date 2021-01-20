# NRSC-5 Binaries

This program receives NRSC-5 (HD Radio) digital radio stations using an RTL-SDR dongle. It offers a command-line interface as well as an API upon which other applications can be built (NRSC5-GUI) Before using it, you'll first need to compile the program using the build instructions below.

### Built using Ubuntu WSL on Windows 20H2. Left the DUSE options as is.
    
    -DUSE_COLOR=OFF       Colorize log output.
    -DUSE_NEON=OFF        Use NEON instructions. (ARM)
    -DUSE_SSE=OFF         Use SSE3 instructions. (x86: Intel P4+ or AMD Athlon XP+)
    -DUSE_FAAD2=ON        AAC decoding with FAAD2.

### NRSC5 Compile script
    
    $ git clone https://github.com/theori-io/nrsc5.git
    $ sudo apt update
    $ sudo apt install mingw-w64 libtool autoconf cmake
    $ support/win-cross-compile 64
    $ support/win-cross-compile 32

### NRSC5-GUI Build

    $ git clone https://github.com/duracell80/nrsc5-gui.git
    $ sudo apt install python3-pip portaudio19-dev libcairo2-dev libgirepository1.0-dev python3-pyaudio mingw-winpthreads
    $ export PATH="$PATH:/Library/Frameworks/Python.framework/Versions/3.8/bin"
    $ pip3 install pillow pygobject pyinstaller
    

Once the build was completed, I copied `libnrsc5.dll` and `nrsc5.exe` from the `build-win32/bin` and the `build-win64/bin` folder to this git.

## Usage

### Command-line options:

       frequency                       center frequency in MHz or Hz
                                         (do not provide frequency when reading from file)
       program                         audio program to decode
                                         (0, 1, 2, or 3)
       -g gain                         gain
                                         (example: 49.6)
                                         (automatic gain selection if not specified)
       -d device-index                 rtl-sdr device
       -p ppm-error                    rtl-sdr ppm error
       -r iq-input                     read IQ samples from input file
       -w iq-output                    write IQ samples to output file
       -o audio-output                 write audio to output WAV file
       -q                              disable log output
       -l log-level                    set log level
                                         (1 = DEBUG, 2 = INFO, 3 = WARN)
       -v                              print the version number and exit
       --dump-aas-files dir-name       dump AAS files
                                         (WARNING: insecure)
       --dump-hdc file-name            dump HDC packets

### Examples:

Tune to 107.1 MHz and play audio program 0:

     $ nrsc5 107.1 0

Tune to 107.1 MHz and play audio program 0. Manually set gain to 49.0 dB and save raw IQ samples to a file:

     $ nrsc5 -g 49.0 -w samples1071 107.1 0

Read raw IQ samples from a file and play back audio program 0:

     $ nrsc5 -r samples1071 0

Tune to 90.5 MHz and convert audio program 0 to WAV format for playback in an external media player:

     $ nrsc5 -o - 90.5 0 | mplayer -

### RTL-SDR drivers on Windows

If you get errors trying to access your RTL-SDR device, then you may need to use [Zadig](http://zadig.akeo.ie/) to change the USB driver. Once you download and run Zadig, select your RTL-SDR device, ensure the driver is set to WinUSB, and then click "Replace Driver". If your device is not listed, enable "Options" -> "List All Devices".


# NRSC5-GUI
NRSC5-GUI is a graphical interface for [nrsc5](https://github.com/theori-io/nrsc5).  
It makes it easy to play your favorite FM HD radio stations using an RTL-SDR dongle.  
It will also display weather radar and traffic maps if the radio station provides them.

## Fork Details
- Added script to produce 2 hour radar loop mp4 of weather maps, ffmpeg required.

## Dependencies

The folowing programs are required to run NRSC5-GUI

* [ffmpeg](https://ffmpeg.org/)
* [Python 3](https://www.python.org/downloads/release)
* [PyGObject](https://pygobject.readthedocs.io/en/latest/)
* [Pillow](https://pillow.readthedocs.io/en/stable/)
* [PyAudio](https://people.csail.mit.edu/hubert/pyaudio/)
* [nrsc5](https://github.com/theori-io/nrsc5)


## Setup
1. Install the latest version of Python 3, PyGObject, Pillow and PyAudio.
2. Compile and install nrsc5.
3. Install nrsc5-gui files in a directory where you have write permissions.

The configuration files will be created in the same directory as nrsc5-gui.py.
An aas directory will be created for downloaded files and a map directory will be created to
store weather & traffic maps in.

## Usage
Open the Settings tab and enter the frequency in MHz of the station you want to play.  
Select the stream (1 is the main stream, some stations have additional streams).  
Set the gain to Auto (you can specify the RF gain in dB in case auto doesn't work for your station).  
You can enter a PPM correction value if your RTL-SDR dongle has an offset.  
If you have more than one RTL-SDR dongle, you can enter the device number for the one you want to use.  

After setting your station, click the play button to start playing the station.  
It will take about 10 seconds to begin playing if the signal strength is good.  
Note: The settings cannot be changed while playing.

### Album Art & Track Info
Some stations will send album art and station logos. These will be displayed in the Album Art tab if available.  
Most stations will send the song title, artist, and album. These are displayed in the Track Info pane if available.  

### Bookmarks
When a station is playing, you can click the Bookmark Station button to add it to the bookmarks list.  
You can click on the name in the bookmarks list to edit it.  
Double click the station to switch to it.  
Click the Delete Bookmark button to delete it.

### Station Info
The station name and slogan is displayed in the Info tab.  
The current audio bit rate is displayed in the Info tab. The bit rate is also shown on the status bar.

#### Signal Strength
The Modulation Error Ratio for the lower and upper sidebands is displayed in the Info tab.  
High MER values for both sidebands indicates a strong signal.  
The Bit Error Rate is shown in the Info tab. High BER values will cause the audio to glitch or drop out.  
The average BER is also shown on the status bar.

### Maps
When listening to radio stations operated by [iHeartMedia](https://iheartmedia.com/iheartmedia/stations),
you can view live traffic maps and weather radar. The maps are typically sent every few minutes and
will be displayed once loaded.  
Clicking the Map Viewer button on the toolbar will open a larger window to view the maps at full size.  
The weather radar information from the last 12 hours will be stored and can be played back by
selecting the Animate Radar option. The delay between frames (in seconds) can be adjusted by changing
the Animation Speed value.

#### Map Customization
The default map used for the weather radar comes from [OpenStreetMap](https://www.openstreetmap.org).
You can replace the map.png image with a map from any website that will let you export map tiles.  
The tiles used are (35,84) to (81,110) at zoom level 8. The image is 12032x6912 pixels.  
The portion of the map used for your area is cached in the map directory.
If you change the map image, you will have to delete the base_map images in the map directory so
they will be recreated with the new map.

### Screenshots
![album art tab](https://raw.githubusercontent.com/cmnybo/nrsc5-gui/master/screenshots/album_art_tab.png "Album Art Tab")
![info tab](https://raw.githubusercontent.com/cmnybo/nrsc5-gui/master/screenshots/info_tab.png "Info Tab")
![settings tab](https://raw.githubusercontent.com/cmnybo/nrsc5-gui/master/screenshots/settings_tab.png "Settings Tab")

![bookmarks tab](https://raw.githubusercontent.com/cmnybo/nrsc5-gui/master/screenshots/bookmarks_tab.png "Bookmarks Tab")
![map tab](https://raw.githubusercontent.com/cmnybo/nrsc5-gui/master/screenshots/map_tab.png "Map Tab")

### Version History
1.0.0 Initial Release  
1.0.1 Fixed compatibility with display scaling  
1.1.0 Added weather radar and traffic map viewer  
2.0.0 Updated to use the nrsc5 API
