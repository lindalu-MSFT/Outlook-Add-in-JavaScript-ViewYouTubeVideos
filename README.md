# Outlook-Add-in-JavaScript-ViewYouTubeVideos

## Summary
This mail app allows users to conveniently view YouTube videos in the app pane in Outlook, if the selected email message or appointment contains a URL to a video on YouTube. The following figure is a screen shot of the YouTube mail app activated for a message in the Reading Pane.
<br />
<br />
![](/static/pic1.png)

## Prerequisites
* Bash
* Ruby 2.2.x+
* OpenSSL
* An email account on Exchange Server 2013
* Client applications that support the mailbox capability in Office 2013, on the desktop and tablet form factors: Outlook 2013, Outlook Web App

## Key components of the sample
* ```/LICENSE.txt``` The terms and conditions of using this distributable
* ```/config.ru``` Rack config
* ```setup.sh``` Setup script to generate ```app.rb```, ```manifest.xml```, and optionally, a certificate
* ```/cert/ss_certgen.sh``` Self-signed certificate generating script
* ```/public/res/js/strings_en-us.js``` US English localization
* ```/public/res/js/strings_fr-fr.js``` French localization

## Description of the code

The main code files for this mail app are ```manifest.xml``` and ```youtube.html```, along with the JavaScript library and string files for apps for Office, and a logo image file. The following is a high level summary of how the mail app works:

This mail app specifies in the ```manifest.xml``` file that it requires a host application that supports the mailbox capability:

```xml
<Capabilities>
    <Capability Name="Mailbox"/>
</Capabilities>
```

In Office 2013, the mailbox capability is supported in the Outlook rich client and Outlook Web App. The mail specifies in the manifest file its support for the desktop and tablet form factors. This further determines that in Office 2013, the applications that can host this mail app are the Outlook rich client and Outlook Web App.

```xml
<DesktopSettings>
    <!-- Change the following line to specify the web server where the HTML file is hosted. -->
    <SourceLocation DefaultValue="https://webserver/YouTube/YouTube.htm"/>
    <RequestedHeight>216</RequestedHeight>
</DesktopSettings>
<TabletSettings>
    <!-- Change the following line to specify the web server where the HTML file is hosted. -->
    <SourceLocation DefaultValue="https://webserver/YouTube/YouTube.htm"/>
    <RequestedHeight>216</RequestedHeight>
</TabletSettings>
```
    
The mail also requests the ReadItem permission in the manifest file, so that it can run regular expressions, which is further discussed below.

```xml
    <Permissions>ReadItem</Permissions>
```
    
The host application activates this mail app when the selected message or appointment contains a URL to a YouTube video. It does so by first reading on startup the manifest.xml file which specifies an activation rule that includes a regular expression to look for such a URL:

```xml
<Rule xsi:type="ItemHasRegularExpressionMatch" PropertyName="BodyAsPlaintext" RegExName="VideoURL" RegExValue="http://(((www\.)?youtube\.com/watch\?v=)|(youtu\.be/))[a-zA-Z0-9_-]{11}"/>
```
    
The mail app defines an initialize function which is an event handler for the initialize event. When the run-time environment is loaded, the initialize event is fired, and the initialize function calls the main function of the mail app, init, as shown in the code below:

```javascript
Office.initialize = function () {
    init(Office.context.mailbox.item.getRegExMatches().VideoURL);
}
```

The ```getRegExMatches``` method of the selected item returns an array of strings that match the regular expression ```VideoURL```, which is specified in the ```manifest.xml``` file. In this case, that array contains URLs to videos on YouTube.

The init function and the rest of the ```youtube.html``` file take as an input parameter that array of YouTube URLs and dynamically build the HTML to display the corresponding thumbnail and details for each video.

This dynamically built HTML displays the first video in a YouTube embedded player, together with details about the video. The app pane also displays the thumbnails of any subsequent videos. The end user can choose a thumbnail to view any of the videos in the YouTube embedded player, without leaving the host application.

## Setup
Shipped with this sample is a ```setup.sh``` - this setup file does the following:
* [Generates the ```manifest.xml```](https://github.com/OfficeDev/Outlook-Add-in-JavaScript-ViewYouTubeVideos/blob/master/setup.sh#L22)
* [Generates the ```app.rb```](https://github.com/OfficeDev/Outlook-Add-in-JavaScript-ViewYouTubeVideos/blob/master/setup.sh#L29)
* [Optionally](https://github.com/OfficeDev/Outlook-Add-in-JavaScript-ViewYouTubeVideos/blob/master/setup.sh#L34) generates a [self-signed certificate](https://github.com/OfficeDev/Outlook-Add-in-JavaScript-ViewYouTubeVideos/blob/master/cert/ss_certgen.sh#L53)

To run the script, type at your POSIX-compliant terminal:

    $ bash setup.sh
    
## Start the server
From the project root, run:

    $ rackup

## Trust your self-signed certificate

Open Safari|
:-:|
![](/static/show_cert.png)|

'Trust' your self-signed certificate|
:-:|
![](/static/add_trust.png)|

## Install the Add-In to Office 365
Installation of this sample Add-In requires access to Outlook on the web.
Installation can be performed from Settings > Manage apps

Manage apps menu|Install from file
:-:|:-:
![](/static/menu_loc.png)|![](/static/menu_opt.png)

Select the manifest.xml|Install and continue
:-:|:-:
![](/static/menu_chooser.png)|![](/static/menu_warn.png)

## See it in action
To demonstrate the functionality of the Add-In, you'll need to use the Office Outlook native client.
* Open your office native client
* Email yourself a link to a YouTube video - Need a [suggestion?](http://www.youtube.com/watch?v=oEx5lmbCKtY)
* Expand the Add-In pane to see a preview

## Questions and comments
* If you have any trouble running this sample, please [log an issue](https://github.com/OfficeDev/https://github.com/OfficeDev/Outlook-Add-in-Javascript-ViewYouTubeVideos/issues).
* Questions about Office Add-in development in general should be posted to [Stack Overflow](http://stackoverflow.com/questions/tagged/office-addins). Make sure that your questions or comments are tagged with [office-addins].

## Additional resources
* [Apps for Outlook](https://dev.office.com/code-samples#?filters=web,outlook)
* [Outlook API](https://dev.outlook.com/)
* [Code Samples - Office Dev Center](https://dev.office.com/code-samples#?filters=web,outlook)
* [Latest News - Office Dev Center](http://dev.office.com/latestnews)
* [Training - Office Dev Center](https://dev.office.com/training)

## Copyright
Copyright (c) 2015 Microsoft. All rights reserved.
