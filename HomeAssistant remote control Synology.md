


```
shell_command:
    stop_eufy: 'ssh -o UserKnownHostsFile=/ssh/known_hosts YOUR_SYNOLOGY_USERNAME@IP_ADDRESS -i /ssh/id_rsa -t “synowebapi --exec api=SYNO.Docker.Container method=“”stop”” version=1 name=“”bropat-eufy-security-ws”””'
```

I also needed to allow password login for root on the Synology NAS (which was disabled by default), so that I can ssh-copy-id the key files to the root@Synology account, then I remove the password login option but only allow ssh keys login.

The fact that it was a Synology NAS is making the process more difficult. (e.g. root password login disabled by default, different command to restart ssh service etc…)

After that I can successfully execute the ssh command remote from HA to Synology.


Source:
https://community.home-assistant.io/t/remote-restart-synology-container-image-using-automation/689698/4
