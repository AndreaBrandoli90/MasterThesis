## HOW TO timestamp with OpenTimestamps Client

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
