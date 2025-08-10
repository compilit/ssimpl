# Multibase Encoding

On the internet, a lot of raw data (bytes) is transported using something called base encoding. These encodings
essentially turn the raw bytes into a piece of transportable and near-readable text. But when you wish to access the raw
bytes again on the receiving side, you need to know which encoding was used in order to decode it. But there are a lot
of different encodings to choose from. This means that encoding and decoding must be well-documented. With Multibase
encoding, a special reserved character for each specific encoding is appended to the encoded value which indicates which
encoding was used. This means that anyone receiving the encoded bytes can always decode it. More can be read
<a href="https://w3c-ccg.github.io/multibase/">here</a>.