### Hi there 👋

<!--
**CheetahProtocal/CheetahProtocal** is a ✨ _special_ ✨ repository because its `README.md` (this file) appears on your GitHub profile.

Here are some ideas to get you started:

- 🔭 I’m currently working on ... Devolping Ecosytem of protocals for solana dev onboarding 
- 🌱 I’m currently learning ...
- 👯 I’m looking to collaborate on ... "NO ROLE MODLES" -J COLE
- 🤔 I’m looking for help with ...
- 💬 Ask me about ... kid with no background in coding use to make fun of em tbh... now in 8 months ive learned solidity then trasitioned into rust "solana"
- 📫 How to reach me: ... microwave#4054 on discord 
- 😄 Pronouns: ... 
- ⚡ Fun fact: ... im tall and like bitches 
-->

ART ENGINE 

must install correct dependances 

install current versions of everything 
-vs code (run admin)
-node.js

funtion 
-npm install
-npm run build


Metaplex

Prerequisites

Following a nice-to-have list:

    Basic knowledge on nodejs / npm
    What Solana is
    What Metaplex is
    An internet connected computer. (the guide has been redacted and tested on a mac)

Step list overview

These are all the steps that we’re gonna follow:

    Build the needed tools, Solana CLI and Metaplex CLI
    Configure the Solana devnet and create a local wallet
    Upload the NFTs and create the Candy Machine
    Create a static website for minting users

Step 1: Build the tools

First of all we need to build ourselves the tools needed to create our candy machine. The two CLIs that we’re gonna use are for Solana and Metaplex.
Solana

To build the Solana CLI we can follow the official instructions:
Install the Solana Tool Suite | Solana Docs
There are multiple ways to install the Solana tools on your computer depending on your preferred workflow: You can…

docs.solana.com

So we need to run the command:

sh -c “$(curl -sSfL https://release.solana.com/v1.7.12/install)"  -for mac


Add the solana command to the $PATH (tutorial here)

And finally check if everything works fine: solana —-version
Metaplex

To install Metaplex we need to run the following commands:

-cd metaplex
-cd js
-yarn install
-yarn bootstrap
-yarn start

And finally check if the CLI is installed correctly: metaplex --version
Create a local wallet and configure the Solana Devnet
Create a wallet

In order to create a local wallet we can run this:

solana-keygen new --outfile ~/.config/solana/devnet.json

Now in the outfile used is stored the keypair for our wallet. Once we’ve set this wallet as default on the Solana config the PubKey can be retrieved with this command: solana-keygen pubkey
Solana configuration

Now we need to set the following configuration:

    the KeyPair that we’ve just created as default
    set the Devnet as the default network, in order to use it as a playground and don’t waste any SOL

solana config set --keypair ~/.config/solana/devnet.json
solana config set --url https://api.devnet.solana.com

So now that the configuration is done, we can

    retrieve our pub key: solana-keygen pubkey
    airdrop some SOL for tests: solana airdrop 1
    get the current wallet balance: solana balance

Upload the NFTs with Metaplex

This is the crucial part of the guide, now we have to upload our NFT to the blockchain with Metaplex.
Folder structure

I suggest to keep all the NFTs resources alone in a single folder, for example a folder structure like the following:

Where the .png are our NFT (extension depends on file type: audio, video, etc ) and the .json are the metadata, please pay attention to keep things arranged correctly:

    same number of .png and .json
    file names starting from 0

The metadata of each NFT must follow this guidelines:
Solana NFT Metadata Standard | Metaplex Docs
Huge thanks to SolFlare for putting this together!# SPL wallets should support the displaying of metadata associated…

docs.metaplex.com
Upload the resources

Now we can upload the NFTs:

metaplex upload ./assets --env devnet --keypair ~/.config/solana/devnet.json --log-level trace --number 4

Let’s breakdown the command:

    ./assets is the folder where the assets are saved
    --env devnet is the environment, in this case devnet
    --keypair ~/.config/solana/devnet.json is the keypair used
    --log-level trace set the log high verbosity
    --number 4 is the total number of the assets currently saved in the folder

The command can take some time uploading all the NFT, depending on the blockchain traffic, your network and the number of NFT.

The upload command do the following:

    upload the .png on arweave
    modify the .json metadata replacing the local images path with the arweave link
    upload the .json on the blockchain
    create the candy-machine configuration

Once the upload completed we have a new file saved in the folder:

Inside the new file we can check the candy machine configuration, for example the file content could be something like:

{
  "program": {
    "uuid": "Ezsai7",
    "config": "Ezsai7EV4oisGhcZHYGGaL4J16ebWKU2yagUevr5fBaS"
  },
  "items": {
    "0": {
      "link": "https://arweave.net/lpFtSkWcdospfV59BQYfy_qUTqY3EbDWDMvTyNMLiE4",
      "name": "TEST 0",
      "onChain": true
    }
  }
}

In this case the candy machine configuration is:

"Ezsai7EV4oisGhcZHYGGaL4J16ebWKU2yagUevr5fBaS"

we’ll need this token later once we setup the candy machine website.
Verify the upload

We can now verify if the resources have been correctly uploaded:

metaplex verify --env devnet --keypair ~/.config/solana/devnet.json> wallet public key: E8ohU8GL8km9yjknPxpaYiRtQCLPwBHevjMjRkFLU2PS
> program id from anchor cndyAnrLdpjq1Ssp1z8xxDsB8dxe7u4HL5Nxi2K5WXZ
> uploaded (4) out of (4)
> ready to deploy!

Perfect, so now let’s create the candy-machine.
Create the Candy Machine

The metaplex command used is:

metaplex create_candy_machine --env devnet --keypair ~/.config/solana/devnet.json --price 2.5 --log-level trace

With this command we can set the price per single NFT. Once our users will mint on the website they’ll pay this amount increased with the metaplex commissions.

Output example:

Setting the log value to: trace
wallet public key: ByuUyvzDF6LyAJsBDxd9HuD2wmkbGc2hdam3Z7Ku45Hp
program id from anchor cndyAnrLdpjq1Ssp1z8xxDsB8dxe7u4HL5Nxi2K5WXZ
create_candy_machine finished.
candy machine pubkey: 9BDeTxMnQ9kiVCgiuPiEaGiyuPmFr7TNHFkbove4CKng

We’ll need later the the CandyMachine pub key to setup the website.
Set the candy machine start date

The last step to publish the candy machine is to setup a start date:

metaplex set_start_date --env devnet -k ~/.config/solana/devnet.json -d "26 Sep 2021 04:20:00 GMT"

Before the live date the candy machine cannot be accessed.
Setup a website

github.com

The only configuration needed is the one save in .env file:

REACT_APP_CANDY_MACHINE_CONFIG=__PLACEHOLDER__
REACT_APP_CANDY_MACHINE_ID=__PLACEHOLDER__
REACT_APP_TREASURY_ADDRESS=__PLACEHOLDER__
REACT_APP_CANDY_START_DATE=__PLACEHOLDER__ REACT_APP_SOLANA_NETWORK=devnet
REACT_APP_SOLANA_RPC_HOST=https://explorer-api.devnet.solana.com

In details:

    REACT_APP_CANDY_MACHINE_CONFIGis the configuration fetched from the .cache/devnet-temp json file, after the upload command
    REACT_APP_CANDY_MACHINE_IDis the candy machine id printed in the terminal after the command crate-candy-machine
    REACT_APP_TREASURY_ADDRESSis where the the minting payments goes, of course this needs to be the same address used when creating the candy machine
    REACT_APP_CANDY_START_DATEis the start date set with the command metaplex set_start_date
    REACT_APP_SOLANA_NETWORK and REACT_APP_SOLANA_RPC_HOST are the two network’s references used, change them once ready to go in production on the mainnet

With this setup we can run the react website and we’ll be able to mint the NFT from the candy machine
