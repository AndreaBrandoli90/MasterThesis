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

  Remember that current implementation of the `otsd` server is not properly daemonized yet, so it will run foreground and not background ( you can skip this issue simply opening another terminal and working on it ). OpenTimestamps server do not distinguish mainnet, testnet or regtest, so you have to specify when running ( from now on we’ll work on regtest ). Remember also that your Bitcoin core must be running already in the chosen mode ( regtest ) and your `bitcoin.conf` ( in order to run the calendar on regtest ) must have the line `rpcport=18443` that is the standard regtest port

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
