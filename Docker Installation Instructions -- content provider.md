# Definitions & General Information

Docker - Docker is a set of platform as a service products that use OS-level virtualization to deliver software in packages called containers. More information at:

https://www.docker.com/

IPFS - IPFS is The InterPlanetary File System is a protocol and peer-to-peer network for storing and sharing data in a distributed file system. More information at:

https://ipfs.io/

Seed - Seed is an identifier of the Stellar wallet. You must create it before you are starting to deal with a dockers

Nickname- Nickname is some user friendly name that is used in MagicWave.

# Prerequisites

## Install docker:

    curl -fsSL https://get.docker.com -o get-docker.sh
    sh get-docker.sh
    chmod +x /usr/bin/docker
    sudo groupadd docker
    sudo usermod -aG docker $USER && newgrp docker

## Docker minimal requirements:

    Memory: 512MB RAM (2GB Recommended). Disk: Sufficient amount to run the Docker containers you wish to use. CPU: 2 cores

## Creating a Stellar wallet and getting a Seed:

1. Download "Stellar Desktop Client" by [link](https://github.com/ripplefox/stellarwallet/releases/)
2. Run the program FoxletStellar
3. Press the button "Create new account" ![image](https://user-images.githubusercontent.com/52072466/178701283-38a695c9-472f-404b-9357-a26862836663.png)
4. Then you need to select the option "Create an empty account" ![image](https://user-images.githubusercontent.com/52072466/178702176-8405ea52-5469-4868-aa85-88d70dc9620d.png)
5. After you need to save the file with the crypto wallet cipher on the PC, this file will be used during each login to the created Stellar crypto wallet.
6. Enter a new password and click on "Encrypt account"
7. Then you need to get the secret key by clicking on the "Show secret key" button. This key is your seed. ![image](https://user-images.githubusercontent.com/52072466/178712306-a3c54982-9310-41d9-bf80-476d92931ea5.png)
   Attention! You must have at least 1 lumen in your account for it to be activated!

## Create workspace directory:

    magicwaveworkspace=<yourworkspacedir>
    mkdir -p ${magicwaveworkspace}
    cd ${magicwaveworkspace}

# Run web site as host

## Create folder for ssl and copy ssl to dir

    magicwaveworkspace=<yourworkspacedir>
    mkdir -p ${magicwaveworkspace}
    cd ${magicwaveworkspace}
    mkdir -p ssl

## If use let's encrypt:

You can find a solution for obtaining a certificate on the official website: https://certbot.eff.org/

The certificate SSL must be a single file (private and public part in one file) in .pem format.

## Pull docker image:

    docker pull magicwaveio/production:ipfs_haproxy-latest

## For host static files

    Get the <cid> of the uploaded video file to the ipfs network. # Downloadable video formats must be supported by the CMS site. For example for wordpress - mp4, m4v, webm, ogv, wmv, flv.
    Set static files:
        cd ${magicwaveworkspace}
        mkdir static
        echo "<html>
                 <head>
                  <meta charset="utf-8">
                  <title>video</title>
                 </head>
                 <body>
                  <video width="400" height="300" controls="controls" controlsList="nodownload" autoplay muted>
                   <source src="/ipfs/insert your cid here">
                  </video>
                   <video width="400" height="300" controls="controls" controlsList="nodownload" autoplay muted>
                   <source src="/ipfs/insert your cid here">
                  </video>
                 </body>
               </html>" > ./static/index.html # or copy your static files
    After starting the docker container, go to the page https://magicwave.{your domain name} from the MagicWave client. 2 added video files are displayed

You can see the example [here](https://magicwave.videotpdemo.com/wp-content/uploads/2022/07/helloworld.html) or download the finished html [here](https://github.com/magicwave-io/docs/blob/master/images/helloworld.html)

## Run docker image:

    cd ${magicwaveworkspace}
    seed=SCR27IGKMKXSOKUV7AC4T3HBTBVBL2MI45HHFSDNRYJFFVKWQAWBBKKZ  # set your seed
    nickname=tum332 # set your nickname

## Run docker

    docker run \
        --name magicwave \
        -e nickname=${nickname} \
        -e seed=${seed} \
        -e http_address=127.0.0.1:80 \
        -e useNginx=1 \
        -e MY_DNS_NAME=<dns client site name> \
        -p 88:80 \
        -p 28000:28080 \
        -p 80:5080 \
        -p 443:5443 \
        -v ${magicwaveworkspace}/tor:/root/tor \
        -v ${magicwaveworkspace}/ipfs:/root/.ipfs \
        -v ${magicwaveworkspace}/ssl:/etc/ssl/application/ \
        -v ${magicwaveworkspace}/hidden_service:/root/hidden_service \
        -v ${magicwaveworkspace}/static:/var/www/html \
        --rm \
        magicwaveio/production:ipfs_haproxy-latest

    To start a docker container in a daemon mode, you need to add the following command to the launch:
    -d

    To access the logs, there is a special docker container command for accessing the logs:
    docker logs <container name>

    The MY_DNS_NAME variable must contain the dns name of the client site. Any name by which the user will go to the site if he has a magicwave client running, or to a redirect site for installing a magicwave client.

## Add text record to DNS:

    1. Echo onion address to console:
    Run command:
    sudo cat ${magicwaveworkspace}/hidden_service/hsv3/hostname

![image](https://github.com/magicwave-io/docs/assets/52072466/5f99cb4e-2008-44e3-b848-49452069c96c)

    2. Add it to DNS TXT records:
    magicwave=<onion address without .onion suffix>
    Godaddy service (https://godaddy.com/):

![image](https://github.com/magicwave-io/docs/assets/52072466/8959ed1b-911d-4628-ae0d-2ab71f1c814d)

    3. Check txt record. Open https://www.whatsmydns.net/#TXT/ Enter your domain name.

## Set up the redirect to the magicwave-is-not-installed page:

To redirect to the page for downloading the MagicWave client, you need to run a docker container on a server with a public IP

    1. Add server IP to DNS A records:

![image](https://github.com/magicwave-io/docs/assets/52072466/5eca099a-db71-46ee-8c3c-6b5bbe0bb055)

    2. Check A record. Open https://www.whatsmydns.net/#A/ Enter your domain name:

![image](https://github.com/magicwave-io/docs/assets/52072466/453ac306-a87b-48bd-ae26-1509729d85e1)

    3. Go to the domain name of a running site, without the magicwave extension enabled:
    There is a redirect to https://magicwave.io/requires/

## Add magicwave subdomain to your domain

    In the domain manager you need to add forward subdomain of magicwave to https://magicwave.io

# Host from another ip or host or localhost site

    This solution is supported on Linux only starting from Docker v20.10.0 released in December 2020.

## Create folder for ssl and copy ssl to dir

    magicwaveworkspace=<yourworkspacedir>
    mkdir -p ${magicwaveworkspace}
    cd ${magicwaveworkspace}
    mkdir -p ssl

## If use let's encrypt:

You can find a solution for obtaining a certificate on the official website: https://certbot.eff.org/

The certificate SSL must be a single file (private and public part in one file) in .pem format.

## Pull docker image:

    docker pull magicwaveio/production:ipfs_haproxy-latest

## Run docker container:

    cd ${magicwaveworkspace}
    seed=SCR27IGKMKXSOKUV7AC4T3HBTBVBL2MI45HHFSDNRYJFFVKWQAWBBKKZ # set your seed
    nickname=tum33212 # set your nickname
    http_address=1.1.1.1:80 # set your webserver ip/name. The port :80 must be entered after ip or domain

## Run docker:

    docker run \
            --name magicwave \
            -e nickname=${nickname} \
            -e seed=${seed} \
            -e http_address=${http_address} \
            -e MY_DNS_NAME=<dns client site name> \
            -p 88:80 \
            -p 28000:28080 \
            -p 80:5080 \
            -p 443:5443 \
            -v ${magicwaveworkspace}/tor:/root/tor \
            -v ${magicwaveworkspace}/ipfs:/root/.ipfs \
            -v ${magicwaveworkspace}/ssl:/etc/ssl/application/ \
            -v ${magicwaveworkspace}/hidden_service:/root/hidden_service \
            --add-host host.docker.internal:host-gateway \
            --rm \
            magicwaveio/production:ipfs_haproxy-latest

    Here is a special execution command: --add-host host.docker.internal:host-gateway the Docker Engine supports communicating with the Docker host via host.docker.internal on Linux.
    This won't work out of the box on Linux because you need to add the extra — add-hostrun flag.
    The host has a changing IP address (or none if you have no network access). We recommend that you connect to the special DNS name host.docker.internal which resolves to the internal IP address used by the host.

    To start a docker container in a daemon mode, you need to add the following command to the launch:
    -d

    To access the logs, there is a special docker container command for accessing the logs:
    docker logs <container name>

    The MY_DNS_NAME variable must contain the dns name of the client site. Any name by which the user will go to the site if he has a magicwave client running, or to a redirect site for installing a magicwave client.

![1_3eCbJFfCH9HJ-dehWvUENg](https://github.com/magicwave-io/docs/assets/52072466/e8c9f5fc-fff6-4f00-8701-2eec36c25db3)

## Add text record to DNS:

    sudo cat ${magicwaveworkspace}/hidden_service/hsv3/hostname

    magicwave=<onion address without .onion suffix>

To check TXT record you can use
https://www.whatsmydns.net/#TXT/magicwave.{domain}

## Set up the redirect to the magicwave-is-not-installed page:

To redirect to the page for downloading the MagicWave client, you need to run a docker container on a server with a public IP

    1. Add server IP to DNS A records:

![image](https://github.com/magicwave-io/docs/assets/52072466/5eca099a-db71-46ee-8c3c-6b5bbe0bb055)

    2. Check A record. Open https://www.whatsmydns.net/#A/ Enter your domain name:

![image](https://github.com/magicwave-io/docs/assets/52072466/453ac306-a87b-48bd-ae26-1509729d85e1)

    3. Go to the domain name of a running site, without the magicwave extension enabled:
    There is a redirect to https://magicwave.io/requires/

## Add magicwave subdomain to your domain

    In the domain manager you need to add forward subdomain of magicwave to https://magicwave.io

# Сhecking sample server

## Install MagicWave client

    1. Go to the site https://magicwave.io/download/
    2. Select the installer for the OS (example "Download for Windows (10+)")
    3. Run installer.
    4. Follow the instructions of the installer.
    5. Start or restart (if open) Сhrome browser.
    6. In the browser message "MagicWave extension add" click on the button Enable extension.

![image](https://github.com/magicwave-io/docs/assets/52072466/e4d13ea4-9b85-486e-8bf1-161908b46183)

    7. Go to https://magicwave.{your domain name}  # There is a successful transition to the site https://magicwave.{your domain name}
    8. Сlick on the MagicWave extension with the left mouse button and switch the switch position to off mode.

![image](https://github.com/magicwave-io/docs/assets/52072466/e9b7e805-05e9-48bf-b6a7-c4ce04404b83)

    9. Go to https://magicwave.{your domain name}  # There is a redirect to the page for requires the MagicWave application https://magicwave.io/requires/

    Magicwave provides both the infrastructure for secure hosting of web sites and the client access infrastructure that allows secure and anonymous access both to web resource hosted on MagicWave and regular internet resources (somewhat similar to VPN, only more secure).
    MagicWave extension supports two modes of operation - partial mode and full mode.
    Switching between operating modes in the extension:

![image](https://github.com/magicwave-io/docs/assets/52072466/cc8f02ae-3bdd-40db-b544-24a7b1adf6b6)

    In partial mode, only access to web resources hosted on MagicWave is done using anonymous and secure infrastructure. Accessing regular websites (e.g. google.com) is done directly, without utilizing MagicWave infrastructure.
    In full mode ALL access is done using MagicWave infrastructure, both to resources hosted on MagicWave and regular internet-hosted resources.
