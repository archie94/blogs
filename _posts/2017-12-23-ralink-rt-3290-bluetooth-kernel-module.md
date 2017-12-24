---
layout: post
cover: 'assets/images/bluetooth_ubuntu_1.jpg'
title: Ralink RT 3290 bluetooth kernel module for linux
date:   2017-12-23 00:00:00
tags: DevOps
subclass: 'post tag-fiction'
categories: 'casper'
navigation: True
logo: 'assets/images/home.png'
---

Bluetooth has been a pain point on my Ubuntu from the start. I did not mind it but now having recently bought a wireless earphones it irks me. A large part of the community suffers from this problem with the Ralink RT 3290 card. WiFi works fine but Bluetooth is always disabled under settings. 

Turns out the installed driver for RT 3290 card is buggy and we are better off using a third party one. Download the latest release of the [driver](https://github.com/loimu/rtbth-dkms). Unzip it. I put the source on Desktop. 

- Update package lists and install kernel header files.

		sudo apt-get update
		sudo apt-get install build-esspeential linux-headers-generic

- Now in our directory (I put it in `~/Desktop/rtbth-dkms-3.9.5`) where we unzipped the source run the following commands

		make
		sudo make install

- Put the source in `/usr/src`

		sudo cp -r ~/Desktop/rtbth-dkms-3.9.5 /usr/src/rtbth-3.9.5

- Dynamically install kernel module 

		sudo dkms install rtbth/3.9.5

- Lastly ask the kernel to load the module at boot time. Add `rtbth` at the end of the file and save it.

		sudo vim /etc/modules

- Reboot your machine. 

This got my bluetooth working. Hopefully it will get yours to work too! Time to enjoy a movie with my earphones XD. 

Ref: [askubuntu](https://askubuntu.com/questions/778615/ralink-rt-3290-bluetooth-problem-on-ubuntu-16-04)

Credits: [blaze](https://github.com/loimu), [N.Sinha](https://askubuntu.com/users/703994/n-sinha)