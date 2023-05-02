# Motion Detection and Surveillance with Raspberry Pi Camera Module v2

A Raspberry Pi-based motion detection and surveillance system that supports email transfer of recordings.

Based on [Picamera2 beta](https://github.com/raspberrypi/picamera2).

Works with the camera module V2 and Debian "bullseye".

Check out how to configure the camera before continuing: [how to configure the camera modules](https://www.raspberrypi.com/documentation/accessories/camera.html#if-you-do-need-to-alter-the-configuration)

## Features

- Motion detection
- Email notifications with video attachments
- Customizable motion sensitivity
- Camera zoom control
- Bandwidth limitation
- Optional local recording deletion after email transfer

## How to run?

### 1) Install Picamera2 package for Python

~~~
sudo apt-get install -y python3-picamera2
~~~

### 2) Run the application

~~~
python3 main.py
~~~

## Start on boot

To start the motion detector on boot, follow these steps:

### 1) `mkdir ~/.config/autostart`

### 2) `nano ~/.config/autostart/MotionDetector.desktop`

### 3) Add the following content to the `.desktop` file:

~~~
[Desktop Entry]
Name=Motion Detector
Type=Application
Comment=RPI motion detection
Exec=sh -c 'sleep 10 && /usr/bin/python3 /path/to/motion_detector.py arguments... > /path/to/logfile 2>&1'
~~~

### 4) Make the file executable: `chmod +x ~/.config/autostart/MotionDetector.desktop`

## Command line arguments

### Enable preview

Shows a preview window of what the camera sees.

~~~
python3 main.py --preview
~~~

### Zoom camera (software-based)

Example of a x2 zoom:

~~~
python3 main.py --zoom 0.5
~~~

### Motion detection sensitivity

If you want the motion detection to be more or less sensitive, you can adjust it as follows:

The lower, the more sensitive.

~~~
python3 main.py --min-pixel-diff 5.2
~~~

### Email transmission of recordings

Sends videos to a specified email.

~~~
python3 main.py --email-username USERNAME --email-password PASSWORD --recipient TO_EMAIL
~~~

### Delete local recordings (after sending them via email)

Deletes the local video after sending them to the recipient email.

~~~
python3 main.py --delete-local-recordings
~~~

## Limit bandwidth

If you run your Raspberry Pi in your home network, you might want to limit the bandwidth. If you don't have a router with QoS, you can use tools like WonderShaper.

Follow these steps to enable traffic shaping on boot:

1) Install WonderShaper
~~~
sudo apt-get install wondershaper
~~~

2) Create the file
~~~
sudo nano /etc/systemd/system/wondershaper.service
~~~

3) Add the content

Here is an example systemd unit file that limits the upload and download speed of the "wlan0" interface to 2048 Kbps:

~~~
[Unit]
Description=Limit UL and DL to 2048kbps

[Service]
Type=simple
ExecStart=wondershaper wlan0 2048 2048

[Install]
WantedBy=multi-user.target
~~~

4) Start the service
~~~
sudo systemctl enable --now wondershaper.service
~~~