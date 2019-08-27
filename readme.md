# btcd for Docker

Docker image that runs btcd in a container for easy deployment.

Dockerfile and start-btcd.sh are based on lighningnetwork docker implementation (https://github.com/lightningnetwork/lnd/tree/master/docker/btcd).

`--addrindex` parameter added to `start-btcd.sh` to support address indexing that can be used to query for specific address.

Transactions can be queried over RPC using the new searchrawtransactions rpc call. It takes a while to create the address index, so wait until it has completed indexing to be able to use this index. Read more about spcific RPC command at https://github.com/btcsuite/btcd/blob/master/docs/json_rpc_api.md#searchrawtransactions

The image contains the latest [btcd](https://github.com/btcsuite/btcd) daemon.

##  Prerequisites

The only requirements for the btcd is to have `Docker` and `Docker Compose` installed, everything else is bundled and provided withing the Docker container.

## Quick Start

1. Build `btcd-node`
		
		docker build --no-cache=false -t btcd-node -f dockerfile .

2. Create a `btcd-data` & `btcd-rpc` volumes to persist the btcd blockchain data and certificates, should exit immediately. The `btcd-data` container will store the blockchain when the node container is recreated (software upgrade, reboot, etc):

        docker volume create --name=btcd-data
        docker volume create --name=btcd-rpc
        
3. Run docker container
        
In production environment change all critital parametes like RPCUSER, RPCPASS, DEBUG, set NETWORK etc.
            
		docker run -d -t --name btcd-node -e RPCUSER=RPCUSER -e RPCPASS=RPCPASS -e NETWORK=testnet -e DEBUG=info -p 18333:18333 -p 127.0.0.1:18334:18334 -v btcd-rpc:/rpc -v btcd-data:/data btcd-node            


4. Verify that the container is running and btcd node is downloading the blockchain

        $ docker ps

5. You can then access the daemon's output thanks to the [docker logs command](https://docs.docker.com/reference/commandline/cli/#logs)

        docker logs -f btcd-node

## JSON RPC API & testing

JSON RPC API should be available on the port 18334. Test availability of API by the curl command or load Postman collection stored in the [etc folder](etc)

	curl -X POST \
	https://localhost:18334 \
	-H 'Accept: */*' \
	-H 'Accept-Encoding: gzip, deflate' \
	-H 'Authorization: Basic UlBDVVNFUjpSUENQQVNT' \
	-H 'Cache-Control: no-cache' \
	-H 'Connection: keep-alive' \
	-H 'Content-Length: 59' \
	-H 'Content-Type: application/json' \
	-H 'Host: localhost:18334' \
	-H 'Postman-Token: 9101469b-6072-4f0d-8d71-d662f256a51e,357f2471-60fd-49a7-94db-c62d46500f23' \
	-H 'User-Agent: PostmanRuntime/7.15.2' \
	-H 'cache-control: no-cache' \
	-d '{"jsonrpc":"1.0","method":"getpeerinfo","params":[],"id":1}'

## Documentation

- Additional documentation in the [docs folder](docs).
