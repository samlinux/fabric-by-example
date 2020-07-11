# Results
< [Back](./readme.md)

## Add (1) 
```bash
# create a copy and a working directory
cd fabric/fabric-samples
cp -r first-network ex01-first-network
cd ex01-first-network

# generate the crypro material
./byfn.sh generate

# start the network
./byfn.sh up  

# Check running container
docker ps 
docker ps --format 'table {{.Names}}\t{{.Image}}'
```
Below you can see that network consists of the following components and can be represented graphically as follows. Try to investigate this.

![byfn overview](Byfn-Overview.png "byfn overview")

## Add (2)
Copy the chaincode to your project folder.
```bash
cp ../chaincode/chaincode_example02/go/chaincode_example02.go
```
![cc_ex02](example_chaincode_02_3.png "cc_ex02")

## Add (3)
Use a terminal multiplexer of your choice (e.g. tmux) to do it. For now we have two methods which make sense to use (invoke and query).

```bash
# exec the cli container
docker exec -it cli bash

# do you first blockchain query
# we can use the use the peer chaincode commands to query 
export CHANNEL_NAME=mychannel
peer chaincode query -C $CHANNEL_NAME -n mycc -c '{"Args":["query","a"]}'

```
Get some information about your current situation and study this environment variables.
```bash
printenv |grep CORE

# we will see the following environment vars
CORE_PEER_LOCALMSPID=Org1MSP
CORE_PEER_ADDRESS=peer0.org1.example.com:7051
CORE_PEER_ID=cli

CORE_PEER_TLS_ENABLED=true
CORE_PEER_TLS_KEY_FILE=/opt/../peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/server.key
CORE_PEER_TLS_CERT_FILE=/opt/../peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/server.crt
CORE_PEER_TLS_ROOTCERT_FILE=/opt/../peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt

CORE_PEER_MSPCONFIGPATH=/opt/../peer/crypto/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp

CORE_VM_ENDPOINT=unix:///host/var/run/docker.sock
```

Invoke the chaincode
```bash
# ---------------------------------------------
# we set some environment vars as placeholders 
# to reduce the cli command
# ---------------------------------------------

export TEST_CHANNEL_NAME=mychannel 

export TEST_CA_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem

export TEST_TLS_ROOT_CERT_ORG1=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt

export TEST_TLS_ROOT_CERT_ORG2=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt

# ------------------------------
# now we can call the commands
# ------------------------------

peer chaincode invoke -o orderer.example.com:7050 --tls true --cafile $TEST_CA_FILE -C $TEST_CHANNEL_NAME -n mycc --peerAddresses peer0.org1.example.com:7051 --tlsRootCertFiles $TEST_TLS_ROOT_CERT_ORG1 --peerAddresses peer0.org2.example.com:9051 --tlsRootCertFiles $TEST_TLS_ROOT_CERT_ORG2 -c '{"Args":["invoke","a","b","10"]}'

# ------------------------------
# notice the difference between peer chaincode query and peer chaincode invoke
# ------------------------------
peer chaincode invoke -o orderer.example.com:7050 --tls true --cafile $TEST_CA_FILE -C $TEST_CHANNEL_NAME -n mycc --peerAddresses peer0.org1.example.com:7051 --tlsRootCertFiles $TEST_TLS_ROOT_CERT_ORG1 --peerAddresses peer0.org2.example.com:9051 --tlsRootCertFiles $TEST_TLS_ROOT_CERT_ORG2 -c '{"Args":["query","b"]}'

```
For now you are only querying Org1 but can you query Org2 as well? And what is needed to do so?

## Add (4)
Stop the network.

```bash 
./byfn.sh down
```
Currently your blockchain is not persistent. What do you have to do to change that?
