## HOW TO connect multiple regtest nodes ( under the same network )

If you want to learn Bitcoin core practically from the command line, the best way is to start the Bitcoin core daemon in regtest mode

```
$ bitcoind -regtest -daemon
```

Regtest mode is good for developers because you do not depend on other nodes and you can set up your private blockchain ad hoc, mining blocks easily on your own with the command ( in the example below we mine 10 blocks )

```
& bitcoin-cli -regtest generate 10
```

In order to get used with all the functionalities of the Bitcoin core, like for example creating, signing and sending transactions, you must manually connect to other nodes running Bitcoin core in regtest mode ( all nodes must be connected on the same network as you are, useful in a class of students )

- First check your ip address

  ```
  $ ip addr show
  ```

  or

  ```
  & ifconfig
  ```

- Then connect your node to another one

  ```
  $ bitcoin-cli -regtest addnode "ip address of the node you want to add" "add"
  ```

- Finally, check if the connection gone successfull

  ```
  $ bitcoin-cli -regtest -getinfo
  ```

  In the section "connections" in the output you must see 1

Do the same way if you want to connect other nodes