# DOCKER OPENVPN ARM AND X86
***
# ARM (RaspberryPi)
#### Upstream Links

* Docker Registry @ [giggio/openvpn-arm](https://hub.docker.com/r/giggio/openvpn-arm/)
* GitHub @ [giggio/docker-openvpn-arm](https://github.com/giggio/docker-openvpn-arm)

## Quick Start

* Pick a name for the `$OVPN_DATA` data volume container, it will be created automatically.

        OVPN_DATA="ovpn-data"

* Initialize the `$OVPN_DATA` container that will hold the configuration files and certificates

        docker volume create --name $OVPN_DATA
        docker run -v $OVPN_DATA:/etc/openvpn --rm giggio/openvpn-arm ovpn_genconfig -u udp://VPN.SERVERNAME.COM
        docker run -v $OVPN_DATA:/etc/openvpn --rm -it giggio/openvpn-arm ovpn_initpki nopass

* Start OpenVPN server process

        docker run -v $OVPN_DATA:/etc/openvpn -d --name openvpn -p 1194:1194/udp --cap-add=NET_ADMIN giggio/openvpn-arm

* Generate a client certificate without a passphrase

        docker run -v $OVPN_DATA:/etc/openvpn --rm -it giggio/openvpn-arm easyrsa build-client-full CLIENTNAME nopass

* Retrieve the client configuration with embedded certificates

        docker run -v $OVPN_DATA:/etc/openvpn --rm giggio/openvpn-arm ovpn_getclient CLIENTNAME > CLIENTNAME.ovpn

## Debugging Tips

* Create an environment variable with the name DEBUG and value of 1 to enable debug output (using "docker -e").

        docker run -v $OVPN_DATA:/etc/openvpn -p 1194:1194/udp --privileged -e DEBUG=1 giggio/openvpn-arm

* Test using a client that has openvpn installed correctly 

        $ openvpn --config CLIENTNAME.ovpn

* Run through a barrage of debugging checks on the client if things don't just work

        $ ping 8.8.8.8    # checks connectivity without touching name resolution
        $ dig google.com  # won't use the search directives in resolv.conf
        $ nslookup google.com # will use search

***

# x86
#### Upstream Links

* Docker Registry @ [kylemanna/openvpn](https://hub.docker.com/r/kylemanna/openvpn/)
* GitHub @ [kylemanna/docker-openvpn](https://github.com/kylemanna/docker-openvpn)

## Quick Start

* Pick a name for the `$OVPN_DATA` data volume container, it will be created automatically.

        OVPN_DATA="ovpn-data"

* Initialize the `$OVPN_DATA` container that will hold the configuration files and certificates

        docker volume create --name $OVPN_DATA
        docker run -v $OVPN_DATA:/etc/openvpn --rm kylemanna/openvpn ovpn_genconfig -u udp://VPN.SERVERNAME.COM
        docker run -v $OVPN_DATA:/etc/openvpn --rm -it kylemanna/openvpn ovpn_initpki nopass

* Start OpenVPN server process

        docker run -v $OVPN_DATA:/etc/openvpn -d -p 1194:1194/udp --cap-add=NET_ADMIN kylemanna/openvpn

* Generate a client certificate without a passphrase

        docker run -v $OVPN_DATA:/etc/openvpn --rm -it kylemanna/openvpn easyrsa build-client-full CLIENTNAME nopass

* Retrieve the client configuration with embedded certificates

        docker run -v $OVPN_DATA:/etc/openvpn --rm kylemanna/openvpn ovpn_getclient CLIENTNAME > CLIENTNAME.ovpn

## `docker-compose`

If you prefer to use `docker-compose` please refer to the [documentation](docs/docker-compose.md).

## Debugging Tips

* Create an environment variable with the name DEBUG and value of 1 to enable debug output (using "docker -e").

        docker run -v $OVPN_DATA:/etc/openvpn -p 1194:1194/udp --privileged -e DEBUG=1 kylemanna/openvpn

* Test using a client that has openvpn installed correctly

        $ openvpn --config CLIENTNAME.ovpn

* Run through a barrage of debugging checks on the client if things don't just work

        $ ping 8.8.8.8    # checks connectivity without touching name resolution
        $ dig google.com  # won't use the search directives in resolv.conf
        $ nslookup google.com # will use search

* Consider setting up a [systemd service](/docs/systemd.md) for automatic
  start-up at boot time and restart in the event the OpenVPN daemon or Docker
  crashes.
