I followed this guide from cedric and the official guide from jitsi, making a few modifications along the way: 
https://www.virtualconfusion.net/jitsi-meet-docker-new-install-2023/
https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-docker/

1. SSH into my Synology and create a folder for jitsi
```
mkdir /volume1/docker/jitsi # root folder for jitsi
mkdir /volume1/docker/jitsi/config # folder for config
mkdir -p /volume1/docker/jitsi/config/{web,transcripts,prosody/config,prosody/prosody-plugins-custom,jicofo,jvb,jigasi,jibri}
```
2. Download and extract the latest release
```
cd /volume1/docker/jitsi
wget $(curl -s https://api.github.com/repos/jitsi/docker-jitsi-meet/releases/latest | grep 'zip' | cut -d\" -f4)
7z x <filename>
cp env.example .env
./gen-passwords.sh
```
3. Update the env file by running `vi .env`
```
CONFIG=/volume1/docker/jitsi/config
ENABLE_AUTH=0 # since I use jitsi in my own network
PUBLIC_URL=https://jitsi.my-quick-connect-id.synology.me
JVB_ADVERTISE_IPS=192.168.1.XXX # This setting deprecates DOCKER_HOST_ADDRESS
:q:TZ=America/Los_Angeles
JVB_COLIBRI_PORT=28080 # To mitigate an docker start failure due to the default 8080 port being used already
```

4. Start the Jitsi docker container
```
sudo docker compose up -d
```
5. By now I'm able to start https://192.168.1.XXX:8443/ and see Jitsi frontend but if I join a meeting the meeting will not connect due to HTTPS not being configured.
6. Update the env file by running `vi .env`
```
DISABLE_HTTPS=1 # since I'll use Synology reverse proxy for HTTPS offloading
```
7. Update my Synology Reverse Proxy Setting
Reverse Proxy Name: jitsi
Source:
  Protocol: HTTPS
  Hostname: jitsi.my-quick-connect-id.synology.me
  Port: 443
Destination:
  Protocol: HTTP
  Hostname: 192.168.1.XXX
  Port: 8000
8. Update port forwarding rules in my gateway (UDM Pro)
Port Forwarding Rule Name: jitsi
Interface: WAN
From: Limited
Source: 192.168.1.0/24
Port: 443
Forward IP: 192.168.1.XXX
Forward Port: 443
9. Start the Jitsi docker container
```
sudo docker compose up -d
```
10. Now I'm able to start Jitsi Meet at https://jitsi.my-quick-connect-id.synology.me/ and everything works fine.
