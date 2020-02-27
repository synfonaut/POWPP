# Proof-of-Work Payment Protocol (POWPP)

`A protocol for paying for proof-of-work`

![Proof of Work](https://media.giphy.com/media/i7mtvabK1CxaM/giphy.gif)

Proof-of-Work payment protocol is a [Bitcoin Schema](https://github.com/synfonaut/MAP/blob/v2/README.md#map-schema) that allows structuring the necessary data to pay for proof-of-work requests and receiving responses, for information from the 21e8 network.

Here's a simple example of requesting proof-of-work and receiving a response

#### POW Request

1. id (UUID)
2. hash (NonceHash)
3. minpow (Integer)
4. payment (RawTx) (optional)
5. snap ([Semantic Nonce Attribute Protocol](https://github.com/synfonaut/SNAP/blob/master/README.md)) (optional)

```json
MAP SCHEMA <POWRequest>
SET
id 123-123-123-123
hash "MAP\nSET\nnamespace\n123-123-123-123\nnonce\n{nonce}"
minpow 2
```

#### POW Response

1. id (UUID)
2. hash (NonceHash)
3. minpow (Integer)
4. pow (Hash)
5. nonce (Integer)
6. txid (Hash) (optional)
7. snap ([Semantic Nonce Attribute Protocol](https://github.com/synfonaut/SNAP/blob/master/README.md)) (optional)

```json
MAP SCHEMA <POWResponse>
SET
id 123-123-123
hash "MAP\nSET\nnamespace\n123-123-123-123\nnonce\n{nonce}"
minpow 2
pow 21e80096c21e2de52d741ac27607e251770c0b9f7e644f684cf37173e871820e
nonce 123123123
```

## POW Request



## id

A `uuid` that identifies this request. It's up to the client to ensure they're generating unique identifiers. POW providers may use different internal ids to identify requests, they'll just hand this back to clients as the identifier that was requested. Ids are required because `hashes` with the same `minpow` may be requested more than once.

```json
MAP SCHEME <POWRequest>
SET
id 123-123-123-123
```



## hash

A `NonceHash` is a hashable string field with a replaceable `{nonce}` identifier. This field is required.

```json
MAP SCHEME <POWRequest>
SET
hash "user/{nonce}"
```



## minpow

The minimum proof-of-work (0's) required for this hash. Default is 0.

```json
MAP SCHEMA <POWRequest>
SET
minpow 2
```



## payment (optional)

A `RawTx` is a signed Bitcoin payment for the proof of work. The output should either be to a designated address, or be signed `SIGHASH_NONE`.


```json
MAP SCHEMA <POWResponse>
SET
id 123-123-124
hash "b9fd30f9a71e332aba0d15626d22e132537c923a1513e9c72c06b1033304af01{nonce}"
minpow 2
pow 21e80096c21e2de52d741ac27607e251770c0b9f7e644f684cf37173e871820e
nonce 123123123
```


## snap (optional)

[Semantic Nonce Attribute Protocol](https://github.com/synfonaut/SNAP/blob/master/README.md) (SNAP) is the data structure that generated the hash. It's optional, but can be useful if you want to reveal the source of the hash. If `snap` is provided but `hash` is not, it is used as the default. 

```json
MAP SCHEMA <POWRequest>
SET
snap "MAP\nSET\nparent\n21e80096c21e2de52d741ac27607e251770c0b9f7e644f684cf37173e871820e\nnonce\n{nonce}"
```



### POW Request Example

Here is a simple request for proof-of-work.

```json
MAP SCHEMA <POWRequest>
SET
id 123-123-123-123
hash "MAP\nSET\nnamespace\n123-123-123-123\nnonce\n{nonce}"
minpow 2
payment 0000100010100101010230123012030123012030123012031023012301023012301203
```



Here's an example where the `hash` is private

```json
MAP SCHEMA <POWRequest>
SET
id 123-123-123-124
hash "b9fd30f9a71e332aba0d15626d22e132537c923a1513e9c72c06b1033304af01{nonce}"
minpow 2
payment 0000100010100101010230123012030123012030123012031023012301023012301203
```



And here the `snap` is revealed which reveals the hidden `hash`.

```json
MAP SCHEMA <POWRequest>
SET
id 123-123-123-125
minpow 2
payment 0000100010100101010230123012030123012030123012031023012301023012301203
snap "MAP\nSET\nnamespace\n123-123-123-123\nnonce\n"
```



Here's an example where the `snap` is provided and no hash.

```json
MAP SCHEMA <POWRequest>
SET
id 123-123-123-126
hash "b9fd30f9a71e332aba0d15626d22e132537c923a1513e9c72c06b1033304af01{nonce}"
minpow 2
payment 0000100010100101010230123012030123012030123012031023012301023012301203
snap "MAP\nSET\nnamespace\n123-123-123-123\nnonce\n{nonce}"
```





## POW Response

Once proof-of-work has been computed it can be sent back.



## id

Same as `id` in request but with `POWResponse` scheme.



## hash

Same as `hash` in request but with `POWResponse` scheme.



### minpow

Same as `minpow` in request but with `POWResponse` scheme.



## pow

The returned proof-of-work hash for this object.

```json
MAP SCHEME <POWResponse>
SET
pow 21e80096c21e2de52d741ac27607e251770c0b9f7e644f684cf37173e871820e
```



### nonce

The `nonce` Integer that generated the proof-of-work.

```json
MAP SCHEME <POWResponse>
SET
nonce 123123123
```



### txid (optional)

A Bitcoin txid hash from the `payment` rawtx, if it was modified by the processor.

```json
MAP SCHEME <POWResponse>
SET
txid b9fd30f9a71e332aba0d15626d22e132537c923a1513e9c72c06b1033304af01
```



## snap (optional)

Same as request `snap` but with `POWResponse` scheme. Not always needed for client since `id` is used to track, but can be useful for forwarding or consistency.



### POW Response Example

Here is a simple response for processed proof-of-work.

```json
MAP SCHEMA <POWResponse>
SET
id 123-123-123
snap "MAP\nSET\nnamespace\n123-123-123-123\nnonce\n{nonce}"
minpow 2
pow 21e80096c21e2de52d741ac27607e251770c0b9f7e644f684cf37173e871820e
nonce 123123123
txid b9fd30f9a71e332aba0d15626d22e132537c923a1513e9c72c06b1033304af01
```



Here's an example where the `snap` is private

```json
MAP SCHEMA <POWResponse>
SET
id 123-123-124
hash "b9fd30f9a71e332aba0d15626d22e132537c923a1513e9c72c06b1033304af01{nonce}"
minpow 2
pow 21e80096c21e2de52d741ac27607e251770c0b9f7e644f684cf37173e871820e
nonce 123123123
```



## Todo

R-puzzles to keep payment safe until puzzle is solved



## Contact

@synfonaut
