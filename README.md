# ERC721 on StarkNet

# Comment: Need to make an easier way to check the status of a deployed contract via voyager. It is easy to check the transaction hash and status directly agains the endpoint, but Voyager takes a long time to register the transaction so the hash is not searchable on voyager almost ever.

## Introduction

Welcome! This is an automated workshop that will explain how to deploy an ERC721 token on StarkNet and customize it to perform specific functions. The ERC721 standard is described [here](https://docs.openzeppelin.com/contracts/3.x/api/token/erc721).
It is aimed at developers that:

- Understand Cairo syntax
- Understand the ERC721 token standard

​
This workshop is the second in a series that will cover broad smart contract concepts (writing and deploying ERC20/ERC721, bridging assets, L1 <-> L2 messaging...).
You can find the first tutorial [here](https://github.com/l-henri/starknet-cairo-101)
Interested in helping writing those? [Reach out](https://twitter.com/HenriLieutaud)!

## Table of contents

- [ERC721 on StarkNet](#erc721-on-starknet)
  - [Introduction](#introduction)
  - [Table of contents](#table-of-contents)
    - [Disclaimer](#disclaimer)
    - [Providing feedback](#providing-feedback)
  - [How to work on this tutorial](#how-to-work-on-this-tutorial)
    - [Before you start](#before-you-start)
    - [Workflow](#workflow)
    - [Checking your progress](#checking-your-progress)
      - [Counting your points](#counting-your-points)
      - [Transaction status](#transaction-status)
      - [Install nile](#install-nile)
        - [With pip](#with-pip)
        - [With docker](#with-docker)
    - [Getting to work](#getting-to-work)
  - [Contract addresses](#contract-addresses)
  - [Points list](#points-list)
    - [ERC721 basics](#erc721-basics)
      - [Exercise 1](#exercise-1)
      - [Exercise 2](#exercise-2)
    - [Minting and burning NFTs](#minting-and-burning-nfts)
      - [Exercise 3](#exercise-3)
      - [Exercicse 4](#exercicse-4)
    - [Adding permissions and payments](#adding-permissions-and-payments)
      - [Exercise 5](#exercise-5)
    - [Minting NFTs with Metadata](#minting-nfts-with-metadata)
      - [Exercise 6](#exercise-6)
      - [Exercise 7](#exercise-7)
​

### Disclaimer

​
Don't expect any kind of benefit from using this, other than learning a bunch of cool stuff about StarkNet, the first general purpose validity rollup on the Ethereum Mainnnet.
​
StarkNet is still in Alpha. This means that development is ongoing, and the paint is not dry everywhere. Things will get better, and in the meanwhile, we make things work with a bit of duct tape here and there!
​

### Providing feedback

Once you are done working on this tutorial, your feedback would be greatly appreciated!
**Please fill [this form](https://forms.reform.app/starkware/untitled-form-4/kaes2e) to let us know what we can do to make it better.**
​
And if you struggle to move forward, do let us know! This workshop is meant to be as accessible as possible; we want to know if it's not the case.
​
Do you have a question? Join our [Discord server](https://discord.gg/B7PevJGCCw), register and join channel #tutorials-support
​

## How to work on this tutorial

### Before you start

The TD has three components:

- An [ERC20 token](contracts/token/ERC20/TDERC20.cairo), ticker ERC721-101, that is used to keep track of points
- An [evaluator contract](contracts/Evaluator.cairo), that is able to mint and distribute ERC721-101 points
- A second [ERC20 token](contracts/token/ERC20/dummy_token.cairo), ticker DTK, that is used to make fake payments

### Workflow

To do this tutorial you will have to interact with the `Evaluator.cairo` contract. To do an exercise you will have to use the `submit_exercise` function to tell the evaluator the address of the evaluated contract. Once it's done you can call the evaluator for it to correct the desired exericse.
For example to solve the first exercise the workflow would be the following:

## Comment: The submit_exercise on the evaluator contract does not exist unless you deploy a new evaluator. For exercise one, using the linked Evaluator here, you need to send the Evaluator your token, or mint directly to the evaluator linked in the readme unless you deploy the evaluator compiled 

`deploy a smart contract that answers ex1` &rarr; `call submit_exercise on the evaluator providing your smart contract address` &rarr; `call ex1_test_erc721 on the evaluator contract`

Your objective is to gather as many ERC721-101 points as possible. Please note :

- The 'transfer' function of ERC721-101 has been disabled to encourage you to finish the tutorial with only one address
- In order to receive points, you will have to reach the calls to the  `distribute_point` function.

## Comment: Below is important: You need to make sure all instructions are in your contract. Use IERCSolution.cairo to check the functions you need. 

- This repo contains an interface `IExerciceSolution.cairo`. Your ERC721 contract will have to conform to this interface in order to validate the exercise; that is, your contract needs to implement all the functions described in `IExerciceSolution.cairo`.
- **We really recommend that your read the [`Evaluator.cairo`](contracts/Evaluator.cairo) contract in order to fully understand what's expected for each exercise**. A high level description of what is expected for each exercise is provided in this readme.
- The Evaluator contract sometimes needs to make payments to buy your tokens. Make sure he has enough dummy tokens to do so! If not, you should get dummy tokens from the dummy tokens contract and send them to the evaluator

### Checking your progress

#### Counting your points

​
Your points will get credited in your wallet; though this may take some time. If you want to monitor your points count in real time, you can also see your balance in voyager!
​

- Go to the  [ERC20 counter](https://goerli.voyager.online/contract/0x0272abeb08a98ce2024b96dc522fdcf71e91bd333b228ad62ca664920881bc52#readContract)  in voyager, in the "read contract" tab
- Enter your address in decimal in the "balanceOf" function

You can also check your overall progress [here](https://starknet-tutorials.vercel.app)
​

#### Transaction status

​
You sent a transaction, and it is shown as "undetected" in voyager? This can mean two things:
​

- Your transaction is pending, and will be included in a block shortly. It will then be visible in voyager.
- Your transaction was invalid, and will NOT be included in a block (there is no such thing as a failed transaction in StarkNet).
​

## Comment: This part is an important troubleshooting step. Highlight this. 

You can (and should) check the status of your transaction with the following URL  [https://alpha4.starknet.io/feeder_gateway/get_transaction_receipt?transactionHash=](https://alpha4.starknet.io/feeder_gateway/get_transaction_receipt?transactionHash=)  , where you can append your transaction hash.
​

#### Install nile


## Comment: Useful commands for Nile found here: https://github.com/OpenZeppelin/nile
## Comment: This needs to be clarified. Set up steps for cairo don't specify you need to 1. run docker daemon and 2. ensure you are in Cairo_VENV before continuing. 

## Comment: Adding Fastecdsa should be done in cairo_venv and on M1 macs may need to specify a CFLAG
## Comment: `CFLAGS=-I/opt/homebrew/opt/gmp/include LDFLAGS=-L/opt/homebrew/opt/gmp/lib pip install fastecdsa`

##### With pip

- Set up the environment following [these instructions](https://starknet.io/docs/quickstart.html#quickstart)

## Comment: Ran fresh install following above instructions. Ran $pip3 install fastecdsa + $brew install gmp + $pip3 install cairo-lang + $python -m pip install --upgrade pip + $pip3 install contextvars
## Comment: Then used alias nile='docker run --rm -v "$PWD":"$PWD" -w "$PWD" lucaslvy/nile:0.7.1' instead of below.  
## Comment: Then ran $python3 utils.py
## Comment: First ran these instructions: https://github.com/OpenZeppelin/nile Did not function successfully.
## Comment: Used https://github.com/starknet-edu/starknet-erc721 and cmd starknet-compile in the starknet-erc721 directory of the root project. (cairo_venv) scottlozano@scoot starknet-erc721 % starknet-compile ./contracts/token/ERC721/ERC721.cairo (ABI was printed to the console). No artifacts generated except one for ERC721.cairo
## Comment: Used $nile compile in same directory. Generated all artifacts. Failed two: contracts/token/ERC721/ERC721_metadata.cairo & contracts/token/ERC721/TDERC721_metadata.cairo 👇🏼
```
Error
Expected one of: "(", ".", ":", "=", "[", "{", operator.
 ecdsa
      ^
```

## Comment: Added to the above contracts (pre-populated) %builtins pedersen range_check ecdsa. Ran $nile compile again. Successful. 
## Comment: Can now call nile deploy and check contract on goerli. 

- Install [Nile](https://github.com/OpenZeppelin/nile).

##### With docker

- Linux and macos

for mac m1: (make sure Docker is running)

```bash
alias nile='docker run --rm -v "$PWD":"$PWD" -w "$PWD" lucaslvy/nile:0.8.0-arm'
```

for amd processors

```bash
alias nile='docker run --rm -v "$PWD":"$PWD" -w "$PWD" lucaslvy/nile:0.8.0-x86'
```

- Windows

```bash
docker run --rm -it -v ${pwd}:/work --workdir /work lucaslvy/0.8.0-x86
```

### Getting to work

- Clone the repo on your machine
- Test that you are able to compile the project

## Comment: current clone of repo directions just state "nile compile" command. 

```bash
nile compile 
```


## Comment: Replace and specify the above direction with: `nile compile contracts/Evaluator.cairo` and then specify you must deploy this contract first using the instructions in /deploy/deploying.txt. Compilation still uses Nile. 

- To convert data to felt use the [`utils.py`](utils.py) script

## Contract addresses

| Contract code                                                        | Contract on voyager                                                                                                                                                             |
| -------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Points counter ERC20](contracts/token/ERC20/TDERC20.cairo)          | [0x0272abeb08a98ce2024b96dc522fdcf71e91bd333b228ad62ca664920881bc52](https://goerli.voyager.online/contract/0x0272abeb08a98ce2024b96dc522fdcf71e91bd333b228ad62ca664920881bc52) |
| [Evaluator](contracts/Evaluator.cairo)                               | [0x03b56add608787daa56932f92c6afbeb50efdd78d63610d9a904aae351b6de73](https://goerli.voyager.online/contract/0x03b56add608787daa56932f92c6afbeb50efdd78d63610d9a904aae351b6de73) |
| [Dummy ERC20 token](contracts/token/ERC20/dummy_token.cairo)         | [0x07ff0a898530b169c5fe6fed9b397a7bbd402973ce2f543965f99d6d4a4c17b8](https://goerli.voyager.online/contract/0x07ff0a898530b169c5fe6fed9b397a7bbd402973ce2f543965f99d6d4a4c17b8) |
| [Dummy ERC721 token](contracts/token/ERC721/TDERC721_metadata.cairo) | [0x02e24bd7683c01cb2e4e48148e254f2a0d44ee526cff3c703d6031a685f1700d](https://goerli.voyager.online/contract/0x02e24bd7683c01cb2e4e48148e254f2a0d44ee526cff3c703d6031a685f1700d) |

## Points list

Today we are creating an animal registry! Animals are bred by breeders. They can be born, die, reproduce, be sold. You will implement these features little by little.

### ERC721 basics



#### Exercise 1


- Create an ERC721 token contract. You can use [this implementation](contracts/token/ERC721/ERC721.cairo) as a base
- Deploy it to the testnet (check the constructor for the needed arguments. Also note that the arguments should be decimals.)

## Comment: Can specify here that the args are from the parameter inputs. Also could specify that you can mint the token directly to the Evaluator as the third arg and save you time transferring. However, given the instructions here, you may not need to actually send the token you create to the contract. Unclear. 

```bash
nile compile contracts/token/ERC721/ERC721.cairo
nile deploy ERC721 arg1 arg2 arg3 --network goerli 
```

## Comment: Needs to explain how to read the Starknet CLI and get the correct info. Currently the contract address is most readable on Voyager. 


- Give token #1 to Evaluator contract // Clarify, where to send? Evaluator contract address. What steps to take before sending. 
- Call `submit_exercise()` in the Evaluator to configure the contract you want evaluated (2 pts)
- Call `ex1_test_erc721()` in the evaluator to receive your points (2 pts)

#### Exercise 2

## Comment: Issues here stem from existing NFTs in wallet. Need to ensure your NFT remains in wallet to be part of the solution evaluated in part B. 

- Call `ex2a_get_animal_rank()` to get assigned a random creature to create.
- Read the expected characteristics of your animal from the Evaluator
- Create the tools necessary to record animals characteristics in your contract and enable the evaluator contract to retrieve them through `get_animal_characteristics` function on your contract ([check this](contracts/IExerciceSolution.cairo))
- Mint the animal with the desired characteristics and give it to the evaluator
- Call `ex2b_test_declare_animal()` to receive points (2 pts)

![Animal](Traits.png)

### Minting and burning NFTs

#### Exercise 3

- Create a function to allow breeders to mint new animals with the specified characteristics
- Call `ex3_declare_new_animal()` to get points (2 pts)

#### Exercicse 4

- Create a function to allow breeders to declare dead animals (burn the NFT)
- Call `ex4_declare_dead_animal()` to get points (2 pts)

### Adding permissions and payments

#### Exercise 5

## Comment: Used cmd $nile deploy dummy_token 0x64746B  0x44544B 100 1 0x01bd14Ef3885921b2ECeb8f5eDd5e8620D29850E8E40c2EC60777A3ab9106714 --network goerli but unsure if this is correct. Kept asking for args. added 1 at end and successful deployment. I think I did it wrong haha. However, I am rich in DTK. Wen DTK yacht? Need to specify in directions to use the pre-deployed ERC20 Dummy token contract as the faucet.  

![Funny mistake](lol.png)



- Use [dummy token faucet](contracts/token/ERC20/dummy_token.cairo) to get dummy tokens
- Use `ex5a_i_have_dtk()` to show you managed to use the faucet (2 pts)
- Create a function to allow breeder registration.
- This function should charge the registrant for a fee, paid in dummy tokens ([check `registration_price`](contracts/IExerciceSolution.cairo))
- Add permissions. Only allow listed breeders should be able to create animals
- Call `ex5b_register_breeder()` to prove your function works. If needed, send dummy tokens first to the evaluator (2pts)

### Minting NFTs with Metadata

#### Exercise 6

- Mint a NFT with metadata on [this dummy ERC721 token](contracts/token/ERC721/TDERC721_metadata.cairo) , usable [here](https://goerli.voyager.online/contract/0x02e24bd7683c01cb2e4e48148e254f2a0d44ee526cff3c703d6031a685f1700d)
- Check it on [Oasis](https://testnet.playoasis.xyz/)
- Claim points on `ex6_claim_metadata_token` (2 pts)

#### Exercise 7

## Comment: Running script for converting metadata is below. Substitute URL for the path to your pinned metadata containing pinned image in the metadata. See folder Images in directory. Converts the length of an array (length = 2 for the url and the .json in the example below). Name files in directory .json and numbered and have link to pinned image in their metadata.

```
PYTHONPATH=. python3 -c "import utils; url='https://gateway.pinata.cloud/ipfs/QmPuwJHUYNYyPuniYu8PQMWxgoWBtpG5ruGViiZUFadTFg/'; print(f'{utils.str_to_felt_array(url)}')" 
```

Above gives array length 3 of converted string and then I converted .json to 199354445678 and tacked it on the end. Used `python3 -i utils.py` and `str_to_felt('.json')` (exit vim is `quit()`)

Successful deployment: 

(This is to manipulate deployment so I don't lose data)

nile deploy ERC721_metadata 0x646F67676F 0x444F47474F 0x01bd14Ef3885921b2ECeb8f5eDd5e8620D29850E8E40c2EC60777A3ab9106714 3 184555836509371486644298270517380613565396767415278678887948391494588524912 181013377130035687865117051153224666913117011459936689029056955402119636277 2281234680916955549700107586580885185257234735 199354445678 --network goerli 
 
 ```  
See ERC721_metadata.cairo for constructor. 

@constructor
func constructor{
        syscall_ptr : felt*,
        pedersen_ptr : HashBuiltin*,
        range_check_ptr
    }(name: felt, symbol: felt, owner: felt, base_token_uri_len: felt, base_token_uri: felt*, token_uri_suffix: felt):
                                           3 (for length of array of baseURL), utils.py convert baseFolderURL, suffix = json (convert with utils.py)
    ERC721_initializer(name, symbol)
    ERC721_Metadata_initializer()
    Ownable_initializer(owner)
    ERC721_Metadata_setBaseTokenURI(base_token_uri_len, base_token_uri, token_uri_suffix)
    return ()
end
```

```
nile deploy ERC721_metadata 0x646F67676F 0x444F47474F 0x01bd14Ef3885921b2ECeb8f5eDd5e8620D29850E8E40c2EC60777A3ab9106714 3 184555836509371486644298270517380613565396767415278678887948391494588524912 181013377130035687865117051153224666913117011459936689029056955402119636277 2281234680916955549700107586580885185257234735 199354445678 --network goerli 
```
- Create a new ERC721 contract that supports metadata. You can use [this contract](contracts/token/ERC721/ERC721_metadata.cairo) as a base
- The base token URI is the chosen IPFS gateway
- You can upload your NFTs directly on [this website](https://www.pinata.cloud/)
- Your tokens should be visible on [Oasis](https://testnet.playoasis.xyz/) once minted!
- Claim points on `ex7_add_metadata` (2 pts)

Deployed contract Here: 0x0771885fa37f2ca1be56a56a05b94383bb484b183534d9b5d798ba873e144efc
​
​
