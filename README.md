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

