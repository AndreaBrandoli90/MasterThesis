# MasterThesis

## Faq OpenTimeStamps

1. **Does the OTS proof reach the block Merkle Tip or just the transaction Merkle Tip ?**

OTS proof reaches the block Merkle Tip. The proof consists in a sequence of commitment operations like "sha256", "append", "prepend". These operations are the cryptographic path that proves that *d* commits to a certain block header. (More info here: <https://en.wikipedia.org/wiki/OpenTimestamps>)

2. ****
