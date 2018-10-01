## HOW TO run a Bitcoin node from command line (Linux based systems)

- OPEN Linux terminal

- SETUP variables ( for an easy installation )

  ```
  $ export BITCOIN=bitcoin-core-0.16.3
  $ export BITCOINPLAIN=`echo $BITCOIN | sed 's/bitcoin-core/bitcoin/'`
  ```

- DOWNLOAD relevant files (REMARK: every time you see user1 replace it with your personal username !!! )

  ```
  $ wget https://bitcoin.org/bin/$BITCOIN/$BITCOINPLAIN-x86_64-linux-gnu.tar.gz -O ~user1/$BITCOINPLAIN-x86_64-linux-gnu.tar.gz
  $ wget https://bitcoin.org/bin/$BITCOIN/SHA256SUMS.asc -O ~user1/SHA256SUMS.asc
  $ wget https://bitcoin.org/laanwj-releases.asc -O ~user1/laanwj-releases.asc
  ```

- VERIFY Bitcoin Signature ( in order to verify your Bitcoin setup is authentic )

  ```
  $ /usr/bin/gpg --import ~user1/laanwj-releases.asc
  $ /usr/bin/gpg --verify ~user1/SHA256SUMS.asc
  ```

  Amongst the info you get back from the last command should be a line telling “Good Signature”, do not care the rest.

- VERIFY Bitcoin SHA (secure hash algorithm) of the .tar file against the expected one

  ```
  $ /usr/bin/sha256sum ~user1/$BITCOINPLAIN-x86_64-linux-gnu.tar.gz | awk '{print $1}'
  $ cat ~user1/SHA256SUMS.asc | grep $BITCOINPLAIN-x86_64-linux-gnu.tar.gz | awk '{print $1}'
  ```

  You must obtain the same number here !

- INSTALL Bitcoin

  ```
  $ /bin/tar xzf ~user1/$BITCOINPLAIN-x86_64-linux-gnu.tar.gz -C ~user1
  $ sudo /usr/bin/install -m 0755 -o root -g root -t /usr/local/bin ~user1/$BITCOINPLAIN/bin/*
  $ /bin/rm -rf ~user1/$BITCOINPLAIN/
  ```

- CREATE ad hoc configuration file ( containing commands executed by Bitcoin daemon when started )

  First, create the directory

  ```
  $ /bin/mkdir ~user1/.bitcoin
  ```

  Then create configuration file

  ```
  $ cat >> ~user1/.bitcoin/bitcoin.conf << EOF
  # Accept command line and JSON-RPC commands
  server=1
  # Username for JSON-RPC connections
  rpcuser=invent_a_username_here
  # Password for JSON-RPC connections
  rpcpassword=invent_a_long_pwd_here
  # Maintain a full transaction index, used by the getrawtransaction rpc call (default: 0)
  txindex=1
  EOF
  ```

  Finally, limit the permission to your configuration file

  ```
  $ /bin/chmod 600 ~user1/.bitcoin/bitcoin.conf
  ```

- START the daemon ( you can run both testnet and regtest on the same machine simply running two different instances of bitcoind )

  TESTNET

  ```
  $ bitcoind -testnet -daemon
  ```

  REGTEST

  ```
  $ bitcoind -regtest -daemon
  ```

- USE bitcoin-cli commands

  Having both daemons active ( one for testnet, the other for regtest ) you can split commands just adding -testnet or -regtest flag to the classic bitcoin-cli commands, for example

  ```
  $ bitcoin-cli -testnet -getinfo
  ```

  This in order to get info about testnet chain and wallet, the same also works for regtest

  ```
  $ bitcoin-cli -regtest -getinfo
  ```

  You can find a full bitcoin-cli command list here: [Command List](<https://en.bitcoin.it/wiki/Original_Bitcoin_client/API_calls_list>)


## HOW TO use OpenTimestamps Client

You can CREATE a timestamp without running a Bitcoin node, to VERIFY you must run a Bitcoin node ( a pruned one is fine too )

- CLONE the GitHub repo on your machine

  ```
  $ git clone https://github.com/opentimestamps/opentimestamps-client.git
  ```

- INSTALL client ( Python3 required )

  ```
  $ pip3 install opentimestamps-client
  ```

- INSTALL the necessary dependencies

  ```
  $ sudo apt-get install python3 python3-dev python3-pip python3-setuptools python3-wheel
  ```

- USE the client

  First move to the respective directory

  ```
  $ cd path_to_dir/opentimestamps-client
  ```

  Create an example file to timestamp

  ```
  $ cat > filename.txt
  bla bla bla ( write what you want )
  CTRL+D ( to close file and return to command prompt )
  ```

  Now you can timestamp your example file this way

  ```
  $ ots stamp filename.txt
  ```

  You must obtain something like this

  ```
  Submitting to remote calendar https://a.pool.opentimestamps.org
  Submitting to remote calendar https://b.pool.opentimestamps.org
  Submitting to remote calendar https://a.pool.eternitywall.com
  Submitting to remote calendar https://ots.btc.catallaxy.com
  ```

  Nice ! Your timestamp request is submitted to the main public calendar servers

  Now in your current directory you must have a file named `filename.txt.ots` that is the proof of your timestamp ( path to the Merkle tip of the respective block header, now incomplete because it requires the assistance of a remote calendar in order to verify ). Let's see

  ```
  $ ots verify filename.txt.ots
  ```

  OpenTimestamps client would respond this way

  ```
  Assuming target filename is 'filename.txt'
  Calendar https://alice.btc.calendar.opentimestamps.org: Pending confirmation in Bitcoin blockchain
  Calendar https://finney.calendar.eternitywall.com: Pending confirmation in Bitcoin blockchain
  Calendar https://bob.btc.calendar.opentimestamps.org: Pending confirmation in Bitcoin blockchain
  Calendar https://btc.calendar.catallaxy.com: Pending confirmation in Bitcoin blockchain
  ```

  Finally, if you want to have all informations regarding your timestamp

  ```
  $ ots info filename.txt.ots
  ```

  If you see something like

  ```
  verify PendingAttestation('https://alice.btc.calendar.opentimestamps.org')
  ```

  This means that that your timestamps is incomplete ( as we said before, so you must wait for Bitcoin blockchain to confirm ).

  You can also upgrade an incomplete timestamp, which adds the path to the Bitcoin blockchain to the timestamp itself

  ```
  $ ots upgrade filename.txt.ots
  ```


## HOW TO run an OpenTimestamps Server locally

You do not need to run a server to use the OpenTimestamps protocol ( there are public servers free to use atm ). However running a server locally can be useful for developers, particularly running the daemon on regtest. The OpenTimestamps server package provides a daemon, called `otsd`. You will need a local Bitcoin node with a non empty wallet ( that’s why regtest is the best choice for practice ) in order to use it and while `otsd` is running the wallet should not be used for other purposes because the Bitcoin timestamp functionality assumes it has exclusive use of the wallet. 

- INSTALL OpenTimestamps server

  First clone the GitHub repo

  ```
  $ git clone https://github.com/opentimestamps/opentimestamps-server.git
  ```

  Then move to the respective directory

  ```
  $ cd path_to_dir/opentimestamps-server
  ```

  And install the requirements

  ```
  $ pip3 install -r requirements.txt
  ```

  Now create your local calendar

  ```
  $ mkdir -p ~/.otsd/calendar/
  $ echo "http://127.0.0.1:14788" > ~/.otsd/calendar/uri
  $ dd if=/dev/random of=~/.otsd/calendar/hmac-key bs=32 count=1
  ```

  REMARK: the URI determines what is put in the URI field of pending attestations returned by this calendar server. For production usage, instead of  "<http://127.0.0.1:14788>” you must put a stable URL that OpenTimestamps clients will be able to access indefinitely

- RUN the server

  Remember that current implementation of the `otsd` server is not properly daemonized yet, so it will run foreground and not background ( you can skip this issue simply opening another terminal and working on it ). OpenTimestamps server do not distinguish mainnet, testnet or regtest, so you have to specify when running ( from now on we’ll work on regtest ). Remember also that your Bitcoin core must be running already in the chosen mode ( regtest ).

  ```
  $ ./otsd --btc-regtest
  ```

  In order to use the server you must tell the client to connect to your personal calendar server. Now you can create a file and stamp it using your calendar server

  ```
  $ cat > testmyserver.txt
  Testing my server!
  CTRL+D
  ```

  Then timestamp it

  ```
  $ ots stamp -c http://127.0.0.1:14788 -m 1 testmyserver.txt
  ```


