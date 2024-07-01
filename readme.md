## Ecowitt WiFi Gateway
*Ecowitt WiFi Gateway driver for Hubitat Elevation*

### Features

- LAN comunication only, no cloud/weather service needed.
- One Hubitat device for each sensor for easy dashboard tiles and RM rules handling.
- Ability to combine outdoor sensors such as the WH32, WH40 and WH68 into a virtual PWS (Personal Weather Station) for rich weather tiles.
- Ability to resynchronize and delete orphaned children at runtime when sensors are unregistered.
- On-the-fly Imperial <-> Metric conversion.
- Accurate sea level pressure correction using altitude and temperature.
- Accurate AQI (Air Quality Index) calculation from PM2.5 density.
- Tile [HTML templates](#templates), including displaying multiple attributes in a single tile. 

### Installation Instructions

#### Ecowitt WS View Mobile App:

1.  Make sure all your sensors are properly registered:  

    <img src="https://github.com/sburke781/ecowitt/raw/main/images/D01.png" width="300" height="600">  

2.  <span>Setup a local/customized weather service, a scheduled push of data from the Gateway to the Hubitat Elevation Hub.  To access this:

    In the WS View App tap "Menu", "Device List" and select (tap) your EcoWitt Gateway device.

    Tap "More", "Weather Services" and keep tapping "Next" until you see a tab titled "Customized"

    Configure the data push with the following settings:
    + Protocol Type is EcoWitt,
    + Server IP / Hostname is the address of your HE Hub,
    + Path is /data,
    + Port is 39501,
    + Set the Upload Interval as you wish, 60 seconds or above is recommended
  
 <span>     
     <img src="https://github.com/sburke781/ecowitt/raw/main/images/D02.png" width="300" height="600">

#### Hubitat: 

1.  If the Ecowitt Gateway has been setup correctly, every time the scheduled data push occurs, you should see the following warning in the Hubitat system log:

    <img src="https://github.com/sburke781/ecowitt/raw/main/images/D03.png">
    
    That's because this driver has not been installed yet and the hub has nowhere to forward the gateway data to.
    
2.  There are two options for installing the EcoWitt Gateway and RF Sensor drivers:
  
    + Option 1 (preferred): [Hubitat Package Manager](https://community.hubitat.com/t/release-hubitat-package-manager-hubitatcommunity/94471), searching for the keyword "EcoWitt" in HPM
    + Option 2 Manual Installation
      + In "Drivers Code" add the Ecowitt [WiFi Gateway](https://raw.githubusercontent.com/sburke781/ecowitt/main/ecowitt_gateway.groovy) and [RF Sensor](https://raw.githubusercontent.com/sburke781/ecowitt/main/ecowitt_sensor.groovy) drivers:

    <img src="https://github.com/sburke781/ecowitt/raw/main/images/D04.png">
    
3.  With the drivers installed, navigate to the "Devices" page on the Hubitat Hub, add a new "Virtual" device, referencing the "Ecowitt WiFi Gateway" driver, then click "Save Device":

    <img src="https://github.com/sburke781/ecowitt/raw/main/images/D05.png">

4.  Enter the EcoWitt Gateway MAC or IP address, in any legal form (MAC addresses are safer because they never change, but they don't work across VLANs) and whether you want to combine outdoor sensors into a PWS (which will allow you to create HTML weather tiles combining attributes from all sensor members).
    Finally "Save Preferences":

    <img src="https://github.com/sburke781/ecowitt/raw/main/images/D06.png">

5.  Wait for the next push of data from the EcoWitt Gateway, configured earlier in the WS View Mobile App.  When this occurs, the driver will automatically create child devices for all the present (and supported) sensors (depending on the frequency you setup for your Gateway to send data, this may take a few minutes):
    
    <img src="https://github.com/sburke781/ecowitt/raw/main/images/D07.png">

### <a name="templates"></a> HTML Templates

HTML templates are a powerful way to present multiple Ecowitt sensor attributes in a single Hubitat dashboard tile.
The following are some basic examples of what you can achieve with a simple HTML template:

<img src="https://github.com/sburke781/ecowitt/raw/main/images/D08.png" width="400" height="235">

Each sensor device can specify up to 5 templates which will allow the creation of 5 customized dashboard tiles.

#### HTML Templates Quick Start

1.  In "Hubitat -> Devices" select an Ecowitt sensor (not the gateway) you'd like to "templetize".
    
    Then In "Preferences -> HTML Tile Template" enter up to five (comma separated) pre-made template IDs which the driver will automatically retrieve from the [template repository](https://github.com/sburke781/ecowitt/blob/main/templates.md).

    Finally click "Save Preferences".
    
    <img src="https://github.com/sburke781/ecowitt/raw/main/images/D09.png">
  
    Note, whenever you click Save Preferences, whether to update the list of Template Id's or any other settings, the html attributes will temporarily disappear until the next feed of data is received from the EcoWitt Gateway.  This will cause existing dashboard tiles to stop displaying the EcoWitt weather readings during this time.

2.  In the Hubitat dashboard you intend to use to create HTML template tiles, click "Cog icon -> Advanced -> CSS" and add the following line to the beginning of the CSS file:
    
     ```  
    @import url("https://sburke781.github.io/ecowitt/html/ecowitt.css");
    ```
     Click "Save CSS"

    <img src="https://github.com/sburke781/ecowitt/raw/main/images/D10.png">   


3.  Now, in the same dashboard, add a new tile, on the left select the Ecowitt sensor, in the center select "Attribute" and on the right select the "html" attribute
    
    <img src="https://github.com/sburke781/ecowitt/raw/main/images/D11.png"> 

    Tip: you can remove the tile "html" title by entering the following in the dashboard CSS (assuming the tile ID is 66):

     ```
    #tile-66 .tile-secondary { visibility: hidden; }
    ```

#### <a name="icons"></a> HTML Template Icons

Using a small true-type font with only specific weather icons, it is possible to add dynamic icons to the Ecowitt dashboard tiles.

For example, using the driver **windCompass** attribute, the following is the syntax to obtain a 360� wind icon which always points to the current wind direction as reported by the Ecowitt gateway:

  ```
  <i class="ewi-wind${windCompass}"></i>
  ```

These icons are in reality text, so of course all the standard CSS font styling, shuch as size, color etc. applies as well.

[This is a complete list](https://sburke781.github.io/ecowitt/html/ecowitt.html) of all the icons available. Just access the page source html to see all the defined icon classes, and how to use them.

<img src="https://github.com/sburke781/ecowitt/raw/main/images/D12.png">

#### <a name="repository"></a> HTML Template Repository

To facilitate reusing and sharing templates, the Ecowitt driver uses a [central JSON repository](https://sburke781.github.io/ecowitt/html/ecowitt.json) where all the templates can be accessed by ID.
This is a [complete up-to-date list](https://github.com/sburke781/ecowitt/blob/main/templates.md) of all the templates available in the repository.

*Templates in the repository are measurement system agnostic and will display the correct unit system based on the parent selection.* 

***

### Disclaimer

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE, TITLE AND NON-INFRINGEMENT. IN NO EVENT SHALL THE COPYRIGHT HOLDERS OR ANYONE DISTRIBUTING THE SOFTWARE BE LIABLE FOR ANY DAMAGES OR OTHER LIABILITY, WHETHER IN CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
