# Run IPFS client

## Pull image

    docker pull magicwaveio/production:ipfs-latest

## Run MagicWave container with IPFS:

## create workspace

    cd ${magicwaveworkspace}
    nickname=tunick21 # set your nickname
    seed=SCR27IGKMKXSOKUV7AC4T3HBTBVBL2MI45HHFSDNRYJFFVKWQAWBBKKZ # set your seed

## run docker container

    docker run \
        --name magicwaveipfs \
        -p 28000:28080 \
        -e nickname=${nickname} \
        -e PP_SINGLEHOP_HS=1 \
        -e seed=${seed} \
        -v ${magicwaveworkspace}/tor:/root/tor \
        -v ${magicwaveworkspace}/ipfs:/root/.ipfs \
        -v ${magicwaveworkspace}/hidden_service:/root/hidden_service \
        --rm \
        magicwaveio/production:ipfs-latest

    To start a docker container in a daemon mode, you need to add the following command to the launch:
    -d

    To access the logs, there is a special docker container command for accessing the logs:
    docker logs <container name>

## Add new files to ipfs

Commands are executed in a new terminal with a running docker container on the computer

    magicwaveworkspace=<yourworkspacedir>
    cd ${magicwaveworkspace}/ipfs
    sudo mkdir -p ./data
    sudo cp ~/<the path to the file that we will upload to the ipfs network> /${magicwaveworkspace}/ipfs/data  # The file that we will upload to the ipfs is copied to the folder
    sudo docker exec -it magicwaveipfs /bin/bash
     ./ipfs add ~/.ipfs/data/<file name>  # After successfully uploading the file to the ipfs, a message is displayed "added <cid> <file name>"
     ./ipfs get {cid}

    # To exit docker after uploading files, press Ctrl+D

## Check uploaded files ipfs:

Copy cid link from previous step.

    Open Chrome browser with MagicWave installed on your computer and follow the link http://localhost:8080/ipfs/{cid}
    Playback or display of the file uploaded to the ipfs will start, if the file format is not supported by the site,
    then it will be downloaded to the computer. Attention!!! This verification will be charged for using the MagicWave network!

# Fill ipfs repository with popular content

Command to fill ipfs repo with popular content:

    ipfs fill

CID - content id in ipfs network (hash of file)
Each node store information abount CID search requests and store in memory count of request (frequent).
Search requests it is DHT request in IPFS network.
Since the data about frequency store only in RAM it will be reset if ipfs will restart.
Ipfs always have some active connection with another network nodes.
Ipfs request information about frequent content from all active connections.
Sort it by frequency for fill repo with most popular content.
If get the same CID from several nodes we set frequent value like sum of frequents.
Ipfs get all most popular content while repo have free space.

For run fill run command open terminal and run:

IPFS_PATH=~/Library/MagicWave/IPFS_data/ /Applications/MagicWave.app/Contents/MacOS/ipfs fill

By the command ipfs will download most popular content while storage will bot full.
Storage limit writen in ipfs config StorageMax by default 10GB
