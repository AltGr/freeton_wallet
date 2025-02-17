
Sub-commands and Arguments
==========================
Common arguments to all sub-commands:


* :code:`-q` or :code:`--quiet`   Set verbosity level to 0

* :code:`--switch STRING`   Set switch

* :code:`-v` or :code:`--verbose`   Increase verbosity level

Overview of sub-commands::
  
  account
    Get account info (local or from blockchain), or create/modify/delete accounts.
  
  call
    Call contracts
  
  client
    Call tonos-cli, use -- to separate arguments. Use 'ft exec -- CMD ARGS' for other commands.
  
  config
    Modify configuration
  
  contract
    Manage contracts
  
  crawler
    Crawl all transactions to an address and fill a psql database
  
  exec
    Call command with substitution on arguments, use -- before the command.
  
  genaddr
    Generate new addr (default is for a SafeMultisigWallet, use 'ft contract --list' for more)
  
  init
    Initialize with TON Labs binary tools, compiled from sources.
  
  inspect
    Inspect information stored on the blockchain: display information on accounts, blocks, messages and transactions.
  
  list
    List known contracts (DEPRECATED). Use 'ft contract --list'
  
  multisig
    Manage a multisig-wallet (create, confirm, send)
  
  node
    Manage local nodes
  
  output
    Perform substitutions on the output
  
  switch
    Display or change current network
  
  test
    For testing only
  
  utils
    Some useful tools
  
  watch
    Monitor a given account for new transactions.


ft account
~~~~~~~~~~~~

Get account info (local or from blockchain), or create/modify/delete accounts.



**DESCRIPTION**


This command can perform the following actions:

* 1.
  Display information on given accounts, either locally or from the blockchain

* 2.
  Create new accounts

* 3.
  Add information to existing accounts

* 4.
  Delete existing accounts


**DISPLAY LOCAL INFORMATION**


Examples:
::

  ft account --list

::

  ft account my-account --info



**DISPLAY BLOCKCHAIN INFORMATION**


Accounts must have an address on the blockchain.

Examples:
::

  ft account my-account

::

  ft account



**CREATE NEW ACCOUNTS**


Examples:
::

  ft account --create account1 account2 account3

::

  ft account --create new-account --passphrase "some known passphrase"

::

  ft account --create new-account --contract SafeMultisigWallet

::

  ft account --create new-address --address 0:1234...


Only the last one will compute an address on the blockchain, since the contract must be known.


**COMPLETE EXISTING ACCOUNTS**


Examples:
::

  ft account old-account --contract SafeMultisigWallet



**DELETE EXISTING ACCOUNTS**


Examples:
::

  ft account --delete account1 account2


**USAGE**
::
  
  ft account ARGUMENTS [OPTIONS]

Where options are:


* :code:`ARGUMENTS`   Name of account

* :code:`--address ADDRESS`   Address for account

* :code:`--contract CONTRACT`   Contract for account

* :code:`--create`   Create new account

* :code:`--delete`   Delete old accounts

* :code:`--info`   Display account parameters

* :code:`--keyfile KEYFILE`   Key file for account

* :code:`--list`   List all accounts

* :code:`--live`   Open block explorer on address

* :code:`--multisig`   Contract should be multisig

* :code:`--passphrase PASSPHRASE`   BIP39 Passphrase for account

* :code:`--surf`   Contract should be TON Surf contract

* :code:`--wc WORKCHAIN`   The workchain (default is 0)

* :code:`--whois ADDRESS`   Returns corresponding key name


ft call
~~~~~~~~~

Call contracts



**DESCRIPTION**


Call a method of a deployed contract. Use --local or --run to run the contract locally (only for get methods). If the params are not specified, {} is used instead. The message is signed if the --sign SIGNER argument is provided, or if the secret key of the account is known.

Examples:
::

  $ ft call giver sendGrams
          '{ "dest":"%{account:address:user1}", "amount":"1000000000000"}'

::

  $ ft --switch mainnet call msig confirmUpdate
          '{  "updateId": "0x6092b3ee656aaa81" }' --sign mywallet


**USAGE**
::
  
  ft call ACCOUNT METH [JSON_PARAMS] [OPTIONS]

Where options are:


* :code:`ACCOUNT METH [JSON_PARAMS]`   arguments

* :code:`-o FILE` or :code:`--output FILE`   Save result to FILE (use - for stdout)

* :code:`--run` or :code:`--local`   Run locally

* :code:`--sign ACCOUNT`   Sign message with account

* :code:`--subst FILE`   Read FILE and substitute results in the content


ft client
~~~~~~~~~~~

Call tonos-cli, use -- to separate arguments. Use 'ft exec -- CMD ARGS' for other commands.



**DESCRIPTION**


This command calls the tonos-cli executable while performing substitutions on arguments, and using the node of the current network switch. It is useful for commands that 'ft' cannot perform directly (calling debots for example).

'ft' uses the executable stored in $HOME/.ft/bin/tonos-cli. To create this executable, use:
::

  $ ft init


or:
::

  $ ft init client


The available substitutions on the arguments can be listed using:
::

  $ ft output --list-subst


For example, to substitute the address of the account 'multisig-debot':
::

  $ ft client -- debot fetch %{account:address:multisig-debot}


Note that it is also possible to ask 'ft' to call 'tonos-cli' instead of performing calls through TON-SDK Rust binding for other commands, using the FT_USE_TONOS=1 env. variable.

**USAGE**
::
  
  ft client -- ARGUMENTS [OPTIONS]

Where options are:


* :code:`-- ARGUMENTS`   Arguments to tonos-cli

* :code:`--exec`   (deprecated, use 'ft exec -- COMMAND' instead)

* :code:`--stdout FILE`   Save command stdout to file


ft config
~~~~~~~~~~~

Modify configuration



**DESCRIPTION**


Change the global configuration or the network configuration.

**USAGE**
::
  
  ft config [OPTIONS]

Where options are:


* :code:`--deployer ACCOUNT`   Set deployer to account ACCOUNT. The deployer is the account used to credit the initial balance of an address before deploying a contract on it.


ft contract
~~~~~~~~~~~~~

Manage contracts



**DESCRIPTION**


This command can perform the following actions:

* 1.
  Build a Solidity contract and store it in the contract database

* 2.
  List known contracts in the contract database

* 3.
  Import a contract into the contract database

* 4.
  Deploy a known contract to the blockchain


**BUILD A CONTRACT**


Example:
::

  ft contract --build Foobar.sol


After this command, the contract will be known as 'Foobar' in the contract database


**LIST KNOWN CONTRACTS**


Example:
::

  ft contract --list


List all known contracts: embedded contracts are contracts that are natively known by 'ft', other contracts are stored in $HOME/.ft/contracts, and were either built or imported by 'ft'.


**IMPORT A CONTRACT**


Example:
::

  ft contract --import src/Foo.tvm


Import the given contract into the contract database. Two files are mandatory: the ABI file and the TVM file. They should be stored in the same directory. The ABI file must use either a '.abi' or '.abi.json' extension, whereas the TVM file must use either '.tvc' or '.tvm. If a source file (.sol, .cpp, .hpp) is also present, it is copied in the database.


**DEPLOY A CONTRACT**


Examples:
::

  ft contract --deploy Forbar


Create an account 'Foorbar', deploy a contract 'Foobar' to it.
::

  ft contract --deploy Forbar --create foo


Create an account 'foo', deploy a contract 'Foobar' to it.
::

  ft contract --deploy Forbar --replace foo


Delete account 'foo', recreate it and deploy a contract 'Foobar' to it.
::

  ft contract --deploy Forbar --create foo --sign admin


Create an empty account 'foo', deploy a contract 'Foobar' to it, using the keypair from 'admin'.
::

  ft contract --deploy Forbar --dst foo


Deploy a contract 'Foobar' an existing account 'foo' using its keypair.



With --create and --replace, 1 TON is transferred to the initial account using a 'deployer' multisig account. The deployer account can either be set switch wide (ft config --deployer 'account') or in the deploy command (using the --deployer 'account' argument)

**USAGE**
::
  
  ft contract [OPTIONS]

Where options are:


* :code:`--build FILENAME`   Build a contract and remember it

* :code:`--create ACCOUNT`   Create ACCOUNT by deploying contract (with --deploy)

* :code:`--deploy CONTRACT`   Deploy contract CONTRACT

* :code:`--deployer ACCOUNT`   Deployer is this account (pays creation fees)

* :code:`--dst ACCOUNT`   Deploy to this account, using the existing keypair

* :code:`--force` or :code:`-f`   Override existing contracts

* :code:`--import CONTRACT`   Deploy contract CONTRACT

* :code:`--list`   List known contracts

* :code:`--new NAME`   Create template file for contract NAME

* :code:`--newi NAME`   Create template file for interface NAME

* :code:`--params PARAMS`   Constructor/call Arguments ({} by default)

* :code:`--replace ACCOUNT`   Replace ACCOUNT when deploying contract (with --deploy)

* :code:`--show-abi CONTRACT`   Show ABI of contract CONTRACT

* :code:`--sign ACCOUNT`   Deploy using this keypair

* :code:`--sol-abi CONTRACT`   Output ABI of contract CONTRACT as Solidity 


ft crawler
~~~~~~~~~~~~

Crawl all transactions to an address and fill a psql database



**DESCRIPTION**


This command will crawl the blockchain and fill a PostgresQL database with all events related to the contract given in argument. The created database has the same name as the account.

This command can run as a service, using the --start command to launch a manager program (that will not detach itself, however), --status to check the current status (running or not) and --stop to stop the process and its manager.

A simple session looks like:
::

  $ ft crawler myapp --start &> daemon.log &
  $ psql myapp
  SELECT * FROM freeton_events;
  serial|                              msg_id                              |      event_name       |           event_args                            |    time    | tr_lt
      1 | ec026489c0eb2071b606db0c7e05e5a76c91f4b02c2b66af851d56d5051be8bd | OrderStateChanged     | {"order_id":"31","state_count":"1","state":"1"} | 1620744626 | 96
  SELECT * FROM freeton_transactions;
  ^D
  $ ft crawler myapp --stop
  


**USAGE**
::
  
  ft crawler ACCOUNT [OPTIONS]

Where options are:


* :code:`ACCOUNT`   Account to crawl

* :code:`--dropdb`   Drop the previous database

* :code:`--start`   Start with a manager process to restart automatically

* :code:`--status`   Check if a manager process and crawler are running

* :code:`--stop`   Stop the manager process and the crawler


ft exec
~~~~~~~~~

Call command with substitution on arguments, use -- before the command.



**DESCRIPTION**


This command can be used to call external commands while performing substitutions on arguments.

The available substitutions on the arguments can be listed using:
::

  $ ft output --list-subst


For example:

$ ft exec -- echo %{account:address:giver}

**USAGE**
::
  
  ft exec -- COMMAND ARGUMENTS [OPTIONS]

Where options are:


* :code:`-- COMMAND ARGUMENTS`   Command and arguments

* :code:`--stdout FILENAME`   Save command stdout to file FILENAME


ft genaddr
~~~~~~~~~~~~

Generate new addr (default is for a SafeMultisigWallet, use 'ft contract --list' for more)



**DESCRIPTION**


DEPRECATED

This command is deprecated and will distributed soon. Use 'ft account' instead.

**USAGE**
::
  
  ft genaddr ARGUMENT [OPTIONS]

Where options are:


* :code:`ARGUMENT`   Name of key

* :code:`--contract STRING`   Name of contract

* :code:`--create`   Create new key

* :code:`--surf`   Use TON Surf contract

* :code:`--wc INT`   WORKCHAIN The workchain (default is 0)


ft init
~~~~~~~~~

Initialize with TON Labs binary tools, compiled from sources.



**DESCRIPTION**


Initialize with TON Labs binary tools, downloading them from their GIT repositories and compiling them (a recent Rust compiler must be installed).

Tools are installed in $HOME/.ft/bin/.

The following tools can be installed:

* 1.
  The 'tonos-cli' client

* 2.
  The 'solc' client from the TON-Solidity-Compiler repository

* 3.
  The 'tvm_linker' encoder from the TVM-linker repository

If no specific option is specified, all tools are generated. If a tool has already been generated, calling it again will try to upgrade to a more recent version.

**USAGE**
::
  
  ft init [OPTIONS]

Where options are:


* :code:`--clean`   Clean before building

* :code:`--client`   Build and install 'tonos-cli' from sources

* :code:`--linker`   Build and install 'tvm_linker' from sources

* :code:`--solc`   Build and install 'solc' from sources


ft inspect
~~~~~~~~~~~~

Inspect information stored on the blockchain: display information on accounts, blocks, messages and transactions.



**DESCRIPTION**


Inspect information stored on the blockchain: display information on accounts, blocks, messages and transactions.

Examples:

Display all transactions that happened on the user1 account:
::

  $ ft inspect --past user1 --with deployed:Contract


The --with argument is used to name the first unknown address, with the name 'deployed' and type 'Contract'. Messages sent to known accounts with known contract types are automatically decoded.

Some operations (--block-num and --head) require to know the shard on which they apply. Arguments --shard SHARD, --shard-block BLOCK_ID and --shard-account ACCOUNT can be used to specify the shard.

Use the FT_DEBUG_GRAPHQL=1 variable to show Graphql queries

**USAGE**
::
  
  ft inspect [OPTIONS]

Where options are:


* :code:`-2`   Verbosity level 2

* :code:`-3`   Verbosity level 3

* :code:`-4`   Verbosity level 4

* :code:`-a ACCOUNT` or :code:`--account ACCOUNT`   Inspect state of account ACCOUNT (or 'all') on blockchain

* :code:`-b BLOCK` or :code:`--block BLOCK`   BLOCK Inspect block TR_ID on blockchain

* :code:`--bn BLOCK_NUM` or :code:`--block-num BLOCK_NUM`   Inspect block at level BLOCK_NUM on blockchain

* :code:`-h` or :code:`--head`   Inspect head

* :code:`--limit NUM`   Limit the number of results to NUM

* :code:`-m MSG_ID` or :code:`--message MSG_ID`   Inspect message with identifier MSG_ID on blockchain

* :code:`-o FILE` or :code:`--output FILE`   Save result to FILE (use - for stdout)

* :code:`--past ACCOUNT`   Inspect past transactions on ACCOUNT on blockchain

* :code:`--shard SHARD`   Block info level/head for this shard

* :code:`--shard-account ACCOUNT`   Block info level/head for this shard

* :code:`--shard-block BLOCK_ID`   Block info level/head for this shard

* :code:`--subst FILE`   Read FILE and substitute results in the content

* :code:`-t TR_ID` or :code:`--transaction TR_ID`   Inspect transaction with identifier TR_ID on blockchain

* :code:`--with ACCOUNT:CONTRACT`   Define partner account automatically defined


ft list
~~~~~~~~~

List known contracts (DEPRECATED). Use 'ft contract --list'



**DESCRIPTION**


DEPRECATED

This command is deprecated and will disappear soon. Use 'ft contract --list' instead.

**USAGE**
::
  
  ft list [OPTIONS]

Where options are:



ft multisig
~~~~~~~~~~~~~

Manage a multisig-wallet (create, confirm, send)



**DESCRIPTION**


This command is used to manage a multisig wallet, i.e. create the wallet, send tokens and confirm transactions.


**CREATE MULTISIG**


Create an account and get its address:
::

  # ft account --create my-account
  # ft genaddr my-account


Backup the account info off-computer.

The second command will give you an address in 0:XXX format. Send some tokens on the address to be able to deploy the multisig.

Check its balance with:
::

  # ft account my-account


Then, to create a single-owner multisig:
::

  # ft multisig -a my-account --create


To create a multi-owners multisig:
::

  # ft multisig -a my-account --create owner2 owner3 owner4


To create a multi-owners multisig with 2 signs required:
::

  # ft multisig -a my-account --create owner2 owner3 --req 2


To create a multi-owners multisig not self-owning:
::

  # ft multisig -a my-account --create owner1 owner2 owner3 --not-owner


Verify that it worked:
::

  # ft account my-account -v



**GET CUSTODIANS**


To get the list of signers:
::

  # ft multisig -a my-account --custodians"



**SEND TOKENS**


Should be like that:
::

  # ft multisig -a my-account --transfer 100.000 --to other-account


If the target is not an active account:
::

  # ft multisig -a my-account --transfer 100.000 --to other-account --parrain


To send all the balance:
::

  # ft multisig -a my-account --transfer all --to other-account



**CALL WITH TOKENS**


Should be like that:
::

  # ft multisig -a my-account --transfer 100 --to contract set '{ "x": "100" }



**LIST WAITING TRANSACTIONS**


Display transactions waiting for confirmations:
::

  # ft multisig -a my-account --waiting



**CONFIRM TRANSACTION**


Get the transaction ID from above, and use:
::

  # ft multisig -a my-account --confirm TX_ID


**USAGE**
::
  
  ft multisig ARGUMENTS [OPTIONS]

Where options are:


* :code:`ARGUMENTS`   Generic arguments

* :code:`-a ACCOUNT` or :code:`--account ACCOUNT`   The multisig account

* :code:`--bounce BOOL`   BOOL Transfer to inactive account

* :code:`--confirm TX_ID`   Confirm transaction

* :code:`--contract CONTRACT`   Use this contract

* :code:`--create`   Deploy multisig wallet on account (use generic arguments for owners)

* :code:`--custodians`   List custodians

* :code:`--debot`   Start the multisig debot

* :code:`--not-owner`    Initial account should not be an owner

* :code:`--parrain`    Transfer to inactive account

* :code:`--req REQ`   Number of confirmations required

* :code:`--src ACCOUNT`   The multisig account

* :code:`--surf`   Use Surf contract

* :code:`--to ACCOUNT`   Target of a transfer

* :code:`--transfer AMOUNT`   Transfer this amount

* :code:`--waiting`    List waiting transactions

* :code:`--wc WORKCHAIN`   The workchain (default is 0)


ft node
~~~~~~~~~

Manage local nodes



**DESCRIPTION**


This command performs operations on nodes running TONOS SE in sandbox networks. It can start and stop nodes, and send tokens to accounts.

**USAGE**
::
  
  ft node [OPTIONS]

Where options are:


* :code:`--give ACCOUNT[:AMOUNT]`   Give TONs from giver to ACCOUNT (use 'all' for user*). By default, transfer 1000 TONS (or AMOUNT) to the account if its balance is smaller, and deploy a contract if it is a multisig smart contract.

* :code:`--start`   Start network node

* :code:`--stop`   Stop network node

* :code:`--web`   Open Node GraphQL webpage


ft output
~~~~~~~~~~~

Perform substitutions on the output



**DESCRIPTION**


This command performs substitutions on its input. By default, the output goes to stdout, unless the '-o' option is used.

Examples:

Load a file INPUT, substitute its content, and save to OUTPUT:
::

  $ ft output --file INPUT --o OUTPUT


List available substitutions:
::

  $ ft output --list-subst


Output address of account ACCOUNT:
::

  $ ft output --addr ACCOUNT


or:
::

  $ ft output --string %{account:address:ACCOUNT}


Output keyfile of account ACCOUNT to file KEYFILE:
::

   ft output --keyfile ACCOUNT -o KEYFILE


**USAGE**
::
  
  ft output [OPTIONS]

Where options are:


* :code:`--addr ACCOUNT`   Output address of account

* :code:`--file STRING`   FILE Output content of file after substitution

* :code:`--keyfile ACCOUNT`   Output key file of account

* :code:`--list-subst`   List all substitutions

* :code:`-o STRING`   FILE Save command stdout to file

* :code:`--string STRING`   FILE Output string after substitution


ft switch
~~~~~~~~~~~

Display or change current network



**DESCRIPTION**


Manage the different networks. Each switch includes a set of accounts and nodes. TONOS SE local networks can be created with this command (see the SANDBOXING section below).


**EXAMPLES**


Display current network and other existing networks:
::

  $ ft switch


Change current network to an existing network NETWORK:
::

  $ ft switch NETWORK


Create a new network with name NETWORK and url URL, and switch to that network:
::

  $ ft switch --create NETWORK --url URL


Removing a created network:
::

  $ ft switch --remove NETWORK



**SANDBOXING**


As a specific feature, ft can create networks based on TONOS SE to run on the local computer. Such networks are automatically created by naming the network 'sandboxN` where N is a number. The corresponding node will run on port 7080+N.

Example of session (create network, start node, give user1 1000 TONs):
::

  $ ft switch --create sandbox1

::

  $ ft node --start

::

  $ ft node --give user1:1000


When a local network is created, it is initialized with:

* 1.
  An account 'giver' corresponding to the Giver contract holding 5 billion TONS

* 2.
  A set of 10 accounts 'user0' to 'user9'. These accounts always have the same secret keys, so it is possible to define test scripts that will work on different instances of local networks.

The 10 accounts are not deployed, but it is possible to use 'ft node --give ACCOUNT' to automatically deploy the account.

**USAGE**
::
  
  ft switch NETWORK [OPTIONS]

Where options are:


* :code:`NETWORK`   Name of network switch

* :code:`--create`   Create switch for a new network

* :code:`--delete`   Remove switch of a network

* :code:`--remove`   Remove switch of a network

* :code:`--url URL`   URL of the default node in this network


ft test
~~~~~~~~~

For testing only


**USAGE**
::
  
  ft test ARGUMENTS [OPTIONS]

Where options are:


* :code:`ARGUMENTS`   args

* :code:`--test INT`   NUM Run test NUM


ft utils
~~~~~~~~~~

Some useful tools



**DESCRIPTION**


Misc commands. For example, to translate bytes from base64 or message boc.

**USAGE**
::
  
  ft utils [OPTIONS]

Where options are:


* :code:`--of-base64 STRING`   Translates from base64

* :code:`--of-boc STRING`   Parse message boc in base64 format


ft watch
~~~~~~~~~~

Monitor a given account for new transactions.



**DESCRIPTION**


Wait for transactions happening on the given ACCOUNT. Transactions are immediately displayed on stdout. If the argument --on-event CMD is provided, a command is called for every event emitted by the contract.

**USAGE**
::
  
  ft watch ACCOUNT [OPTIONS]

Where options are:


* :code:`ACCOUNT`   Watch account ACCOUNT

* :code:`-0`   Verbosity level none

* :code:`-3`   Verbosity level 3

* :code:`--account ACCOUNT`   Watch account ACCOUNT

* :code:`--from BLOCKID`   Start with block identifier BLOCKID

* :code:`-o FILE` or :code:`--output FILE`   Output to FILE

* :code:`--on-event CMD`   Call CMD on event emitted. Called once on startup as `CMD <block_id> start` and after every emitted event as `CMD <block_id> <tr_id> <event_name> <args>`

* :code:`--timeout TIMEOUT`   Timeout in seconds (default is 25 days)
