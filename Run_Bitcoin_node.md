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