# OpenConnect VPN Client for MikroTik Devices (arm64v8)

This Docker image provides an OpenConnect VPN client tailored for use under MikroTik ARM devices.

## Usage Prerequisites

1. **Container mode** must be enabled on your MikroTik device. You can find information on Docker support for MikroTik [here](https://wiki.mikrotik.com/wiki/Container).
2. If your device doesn't have enough space on flash, you will need to use an additional flash disk. In the configuration, it will be mounted as disk1.

## Environment Variables

Add the following environment variables:
```
/container envs add key=ANYCONNECT_PASSWORD name=openconnect value="password"
/container envs add key=ANYCONNECTSERVER name=openconnect value="serverurl"
/container envs add key=ANYCONNECT_USER name=openconnect value="user"
/container envs add key=ANYCONNECT_GROUP name=openconnect value="group"
```
> **Note:** 
If your server has SSL issues (you may see a log error: 20D8FF76:error:0A000152:SSL routines:final_renegotiate:unsafe legacy renegotiation disabled:ssl/statem/extensions.c:892:), you can add the following environment variable **at your own risk**:
```
/container envs add key=ANYCONNECT_KEY name=openconnect value="--allow-insecure-crypto"
```
## Setting Registry URL and Extract Directory

Set the registry URL (for downloading containers from Docker registry) and set the extract directory (tmpdir) to the attached USB media:
```
/container/config/set registry-url=https://registry-1.docker.io
/container/config/set tmpdir=disk1/pull
```
> **Note:** Change disk1 to your attached flash disk name.

## Pulling the Image

To pull the image, run:
```
/container add remote-image=tmasnyk/mikrotik-openconnect:latest interface=veth1 envlist=openconnect root-dir=disk1/containers/openconnect start-on-boot=yes hostname=openconnect logging=yes
```
## Starting the Container

To start the container, use:
```
/container start 0
```
## Quick Configuration Steps

1. Install the container package.
2. Enable container mode:
```
/system/device-mode/update container=yes
```
You will need to confirm the device mode by pressing the reset button.

4. Copy the configuration, change environment variables, and update disk1 to your attached flash disk name.
5. Open a new terminal and paste the following commands:
```
/interface/veth/add name=veth1 address=172.17.0.2/24 gateway=172.17.0.1
/interface/bridge/add name=containers
/ip/address/add address=172.17.0.1/24 interface=containers
/interface/bridge/port add bridge=containers interface=veth1
/ip/firewall/nat/add chain=srcnat action=masquerade src-address=172.17.0.0/24
```
6. Re-add the environment variables as previously mentioned.

7. Set the registry URL and temporary directory again:

8. Finally, pull the image again:
```
/container add remote-image=tmasnyk/mikrotik-openconnect:latest interface=veth1 envlist=openconnect root-dir=disk1/containers/openconnect start-on-boot=yes hostname=openconnect logging=yes
```
9. Start the container:
```
/container start 0
```   
Now you have successfully set up the OpenConnect VPN client on your MikroTik device!

   
