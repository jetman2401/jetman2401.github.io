## 1. Create a DigitalOcean.com account
Go to the following link to sign-up for a DigitalOcean account that will have $200 of account credit once the sign-up process is complete: https://m.do.co/c/4d7f4ff9cfe4
	Note: if you already have an account, follow the link to create one anyway because the link is the only way to get the $200 credit, unless you want to pay for any DO products you use
## 2. Create an Ubuntu 20.04 Droplet
1. Navigate to the DO Control Panel and in the Pane that pops up on the left side of the screen, under Manage, click Droplets. Click the Blue Create Droplet button that appears in the middle of the screen.
2. The following are the settings you should choose for expediency and cost-effectiveness before deploying your Droplet:
	1. Region: choose one of the datacenter regions in San Francisco
	2. OS: choose the most recent version of Ubuntu
	3. Plan: Shared CPU, Basic plan; Regular with SSD CPU option; choose the most inexpensive Plan card that isn't grayed out due to a OS size issue, which in our case is this: $6/mo, $0.009/hour, 1 GB/1 CPU, 25 GB SSD Disk, 1000 GB transfer
	4. Authentication: if you have an SSH key, use it because it provides greater security than a password. Otherwise, create a password to use on with the Droplet
	5. All other options can remain as the default for this Project
3. Click Create Droplet. Your Droplet will take a minute or two to provision.
4. Click the Droplet to go into the Control Panel for it
## 3. Install Docker
1. You now need to SSH into your new Droplet. To do so, go into the Networking tab of the Control Panel of your Droplet and click the number labeled PUBLIC IPV4 ADDRESS. This should copy it to your clipboard. Then, use PowerShell, Command Prompt, or PuTTY to SSH in (I used PuTTY). You will log in as root and the password will be the password you set while choosing settings for the Droplet
Note: for the following steps in which I install Docker, I will be using the instructions on https://thematrix.dev/install-docker-and-docker-compose-on-ubuntu-20-04/
2. Type the following commands in EXACTLY the order that they are listed:
~~~
sudo apt install apt-transport-https ca-certificates curl software-properties-common -y

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

sudo add-apt-repository \ "deb [arch=amd64] https://download.docker.com/linux/ubuntu \ $(lsb_release -cs) \ stable"

apt-cache policy docker-ce

sudo apt install docker-ce -y

sudo curl -L "https://github.com/docker/compose/releases/download/1.27.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

sudo chmod +x /usr/local/bin/docker-compose
~~~
## 4. Install Wireguard
Note: for the following steps in which I actually install and configure the Wireguard VPN, I will be using the instructions on https://thematrix.dev/setup-wireguard-vpn-server-with-docker/
1. Run the following commands in the EXACT order in which they are written:
	'mkdir -p ~/wireguard/'
	'mkdir -p ~/wireguard/config/'
	'nano ~/wireguard/docker-compose.yml'
2. Once you are using a Text Editor to view the file you have created, paste the following into the file:
~~~
version: '3.8'
services:
  wireguard:
    container_name: wireguard
    image: linuxserver/wireguard
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=America/Chicago
      - SERVERURL=143.198.239.121
      - SERVERPORT=51820
      - PEERS=pc1,pc2,phone1
      - PEERDNS=auto
      - INTERNAL_SUBNET=10.0.0.0
    ports:
      - 51820:51820/udp
    volumes:
      - type: bind
        source: ./config/
        target: /config/
      - type: bind
        source: /lib/modules
        target: /lib/modules
    restart: always
    cap_add:
      - NET_ADMIN
      - SYS_MODULE
    sysctls:
      - net.ipv4.conf.all.src_valid_mark=1
~~~
	Note: the SERVERURL depends on DigitalOcean; you will have a different URL than me and should change SERVERURL to reflect that
	Use Ctrl+X to save and exit the Text Editor
3.  Run the following two commands in the order that they are listed:
	'cd ~/wireguard/'
## 5a. Test your VPN on a mobile device
1. Before we turn on Wireguard, we first look at our IP address without it using IPleak.net:
![WG_7](https://user-images.githubusercontent.com/117373527/205385969-ebb6e64e-2e0c-4e70-9905-fd82d2e89d6e.jpg)
2. 2. Now, we use this command: 'docker-compose up -d' . This will start Wireguard
3. To connect our phone to wireguard, we will enter the following command: 'docker-compose logs -f wireguard' . This will print out three QR codes. If this doesn't work for some reason, you can also cd into peer_phone1 and type 'qrencode -t ansiutf8 < peer_phone1.conf' . Download the Wireguard app, and open it. Once it is open, click the plus symbol:
![WG_8](https://user-images.githubusercontent.com/117373527/205385979-691e1d47-84e1-482e-b421-816ec57847cb.jpg)
	Then, tap the SCAN FROM QR CODE button:
![WG_9](https://user-images.githubusercontent.com/117373527/205385999-da336363-5379-4743-9311-e0e8e42dbc27.jpg)
	Scan the QR code on your SSH and then on your phone type in a name for your tunnel. After this has all been done, it should look like this on your phone:
![WG_10](https://user-images.githubusercontent.com/117373527/205386023-3cf71166-5a1e-4fab-9256-7fc050d76a5d.jpg)
	And if you tap on the tunnel, it should you more information about it like so:
![WG_12](https://user-images.githubusercontent.com/117373527/205386034-a671dc0d-fe31-4658-a0af-5ae4c6abf549.jpg)
4. Tap the slider to turn on Wireguard, then go back to IPleak.net and refresh it. It should now show a different IP address like so:
![WG_11](https://user-images.githubusercontent.com/117373527/205386047-f6d7943b-cf9a-46f1-83cc-d93858829598.jpg)
## 5b. Test your VPN on a laptop
1. Before we turn on Wireguard, we first look at our IP address without it using IPleak.net:
![WG_1](https://user-images.githubusercontent.com/117373527/205385764-22b43a28-02e1-4649-87bb-45aebc3bd1df.png)
2. Now, we use this command: 'docker-compose up -d' . This will start Wireguard
3. To connect our first PC to Wireguard, first we go to the following location: 'cd ~/wireguard/config/peer_pc1' . Once we have changed directory, we switch applications to WinSCP
4. In WinSCP, we use the public IPv4 address provided by DO, root as the username, and the password we set when creating the Droplet. Then, we log in
![WG_2](https://user-images.githubusercontent.com/117373527/205385788-3e2a5c22-b4fc-4d03-a6fb-cd42edd1bca5.png)
5. Now we double-click wireguard followed by config, and finally peer_pc1. We then right-click peer_pc1.conf and click Download...
![WG_3](https://user-images.githubusercontent.com/117373527/205385811-ce2ea777-6dcb-42b8-9ed9-b4fb15221032.png)
	This should then give you the option of choosing where you save the file. REMEMBER WHERE YOU SAVE THIS FILE
6. Download Wireguard off of the Internet. Once this is done, open Wireguard and click Add Tunnel. You will then, using the screen that pops up, direct Wireguard to the .conf file you just downloaded from your Droplet. Once all is said and done, your Wireguard should look like this:
![WG_4](https://user-images.githubusercontent.com/117373527/205385915-18b75ab0-c24f-4ba7-a25f-ae2bf909f69f.png)
	Click Activate and then your Wireguard will look something like this:
![WG_6](https://user-images.githubusercontent.com/117373527/205385934-65628d73-7eb5-4109-b845-09300d6e5d7b.png)
7. Refresh IPleak.net, and make sure your IP address is different:
![WG_5](https://user-images.githubusercontent.com/117373527/205385947-8decfb37-86e4-4553-9228-05e145e2ea4f.png)
