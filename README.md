# Raspberry Pi Urban Mobility Tracker

## Hardware

### Primary Components
1) [Raspberry Pi 4b](https://www.raspberrypi.org/products/raspberry-pi-4-model-b)
2) [Raspberry Pi Camera V2](https://www.raspberrypi.org/products/camera-module-v2)
 
## Install (from macOS)
1) Insert memory card
2) Install [Raspberry Pi Imager](https://www.raspberrypi.com/software/)
	- Select `Raspberry Pi OS (other)`
	- Select `Raspberry Pi OS Lite (32-bit)`
	- Make note of hostname
	- Enable SSH
	- Set username and passwod
	- Configure wireless LAN
	- Burn the image to the memory card
3. Insert memory card in to PRi and turn on
4. `ssh pi@raspberrypi.local`
5. `sudo raspi-config`
	- Enable legacy camera support
	- Add any additional WiFi credentials you may need
	- Check for updates
6. `sudo apt-get update && sudo apt-get upgrade`
7. Install Docker
	- `curl -fsSL https://get.docker.com -o get-docker.sh`
	- `sudo sh get-docker.sh`
	- `rm get-docker.sh`
8. Add non-root user to Docker user group `sudo usermod -aG docker pi`
9. Create UMT output directory and move into it `mkdir umt_output && cd umt_output`
10. Download the Dockerfile `wget https://raw.githubusercontent.com/TravelByRocket/bikestreets-umt/master/Dockerfile`
11. Download a replacement Python file `wget https://raw.githubusercontent.com/TravelByRocket/bikestreets-umt/master/generate_detections.py`
12. Build the Docker container `sudo docker build . -t umt` 
	- or `sh build_container.sh` if script downloaded
14. Start the Docker container: `docker run -it --privileged --mount type=bind,src=/home/pi/umt_output,dst=/root -d --restart unless-stopped umt`
	- or `sh run_container.sh` if script downloaded
	- `-d` start the container detached
	- attach container with `docker attach CONTAINER_NAME`
	- get container name from `docker ps` or simply type `docker attach ` and press `Tab` button for auto-completion
	- detach container with `Ctrl-P Ctrl-Q`

## Usage
Since this code is configured as a cli, everything is accessible via the `umt` command on your terminal. To run while using the Raspberry Pi camera (or laptop camera) data source run the following:
``` sh
umt -camera
```
To run the tracker on an image sequence, append the `-imageseq` flag followed by a path to the images. Included in this repo are the first 300 frames from the MOT (<a target="_blank" href="https://motchallenge.net/">Multiple Object Tracking Benchmark</a>) Challenge <a target="_blank" href="https://motchallenge.net/vis/PETS09-S2L1">PETS09-S2L1</a> video. To use them, simply download/clone this repo and cd into the main directory.
```sh
umt -imageseq data/images/PETS09-S2L1/
```
To view the bounding boxes and tracking ability of the system, append the `-display` flag to view a live feed. Note that this will greatly slow down the fps and is only recommended for testing purposes.
```sh
umt -imageseq data/images/PETS09-S2L1/ -display
```
By default, only the first 10 frames will be processed. To increase or decrease this value, append the `-nframes` flag followed by an integer value.
```sh
umt -imageseq data/images/PETS09-S2L1/ -display -nframes 20
```
To persist the image frames and detections, use the `-save` flag. Saved images are then available in the `output/` directory.
```sh
umt -imageseq data/images/PETS09-S2L1/ -save -nframes 20
```
To run the tracker using a video file input, append the `-video` flag followed by a path to the video file. Included in this repo are two video clips of vehicle traffic.
```sh
umt -video data/videos/highway_01.mp4
```
In certain instances, you may want to override the default object detection threshold (default=0.5). To accompish this, append the `-threshold` flag followed by a float value in the range of [0,1]. A value closer to one will yield fewer detections with higher certainty while a value closer to zero will result in more detections with lower certainty. It's usually better to error on the side of lower certainty since these objects can always be filtered out during post processing.
```sh
umt -video data/videos/highway_01.mp4 -display -nframes 100 -threshold 0.4
```
To get the highest fps possible, append the `-tpu` flag to use the Coral USB Accelerator for inferencing.
```sh
umt -imageseq data/images/PETS09-S2L1/ -tpu
```

## References
```
@inproceedings{Wojke2017simple,
  title={Simple Online and Realtime Tracking with a Deep Association Metric},
  author={Wojke, Nicolai and Bewley, Alex and Paulus, Dietrich},
  booktitle={2017 IEEE International Conference on Image Processing (ICIP)},
  year={2017},
  pages={3645--3649},
  organization={IEEE},
  doi={10.1109/ICIP.2017.8296962}
}

@inproceedings{Wojke2018deep,
  title={Deep Cosine Metric Learning for Person Re-identification},
  author={Wojke, Nicolai and Bewley, Alex},
  booktitle={2018 IEEE Winter Conference on Applications of Computer Vision (WACV)},
  year={2018},
  pages={748--756},
  organization={IEEE},
  doi={10.1109/WACV.2018.00087}
}
```
