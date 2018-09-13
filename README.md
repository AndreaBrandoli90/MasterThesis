# MasterThesis

## Faq OpenTimeStamps

1. **Does the OTS proof reach the block Merkle Tip or just the transaction Merkle Tip ?**

OTS proof reaches the block Merkle Tip. The proof consists in a sequence of commitment operations like "sha256", "append", "prepend". These operations are the cryptographic path that proves that *d* commits to a certain block header. (More info here: <https://en.wikipedia.org/wiki/OpenTimestamps>).

Another confirmation of this fact is in the article of P.Todd: [OpenTimestamps: Scalable, Trust-Minimized, Distributed Timestamping with Bitcoin](https://petertodd.org/2016/opentimestamps-announcement) which says: "*Each per-file timestamp is simply the list of commitment operations that comprise the path up the first merkle tree, then up the Bitcoin block’s merkle tree, to finally get to the block header.*"

2. ****
