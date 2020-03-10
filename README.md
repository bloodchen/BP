# BP://

B Plus file protocol

## Why

The B:// protocol by unwriter is widely adopted by BSV community and Apps. With the genesis update, we can expect more and more files saved on blockchain. There are some feedbacks from the community, hoping some features be added to B protocol. But in order to keep the compatibility of B protocol with current data and Apps, it's better to design a new protocol that embrace these features.

## Features

1. **Hash of the file be saved in the protocol** (Proposed by JeffChen)
   * With the hash saved with the file data, it will enable client to verify the originality of the file. It will be very helpful in scenarios where security is important.
   
   * The cost of data on blockchain is still high compared to triditional storage. With Hash of file saved, it's easy to avoid file duplications. A client can query the hash before uploading the file.
   
   * If the hash added to protocl, it's easier to use current infrastructure, like bitquery, to use it,  Other than building something similar to C:// protocol, which is not part of the TX record.

2. **Content Compression** (Proposed by Satchmo)
	
	* Supporting compression will dramaticly reduce the storage required onchain.
	
		*Note1: When the Data is compressed, the HASH is the HASH of the compressed Data itself.*
		
		*Note2: When compress file with gzip, files with same content may preduce different gzip. You may want to use -n parameter to avoid that. [more](https://medium.com/@mpreziuso/is-gzip-deterministic-26c81bfd0a49)*
	
3. **Use JSON as Attibute Format** (Proposed by JeffChen)
	* Using output push data as protocol atttribute is execllent for querying, since bitquery can query push data as out.s1,out.s2 ... easily. But it's not flexible especially when there are optional attributes. Using push data also fixes ordering of the attributes which is not necessary.
	
	* BP protocol will use JSON in one push data to present some attribute of the protocol. Please note, only attributes that are not supposed to query against will be saved here.

...

## Design

The BP protocol is similar to B protocol

```
OP_0 OP_RETURN "1NsP5Sb9bSKR4D6vJT3h1as2Lq5YfphZYn" [Data] "Attributes" "sha256 of Data"
```
Where Attributes is a JSON string 

```
{
	"mediatype":"text/html",	//optional, default is
	"encoding":"binary",		//optional, default is binary
	"filename":"index.html",  //optional
	"compression":"gzip"		//optional, default is no compression
}
```


## Referenceing Data

Since every file in BP protocol has hash with it, we could reference the file using it's hash.

Let's suppose index.html's hash is 157957eb898913471dd77ca132e2d2e51f391e2e0e7c24fea9dec883372b073e, then you can reference it as 

```
bp://5a8260771924b1c88af8d247d18b8b954222b0efedfd5b45ef3f8158172687a8
```

If the html file has links to other files, it can also be referenced as BP file.

```
<html>
<body>
<img src="bp://157957eb898913471dd77ca132e2d2e51f391e2e0e7c24fea9dec883372b073e"
<!-- src="dragon.jpg" -->
</body>
</html>
```

## Requesting Data

Anyone can use bitquery to request data from a bitquery endpoint. For example, the above dragon.jpg

```
{
  "v": 3,
  "q": {
    "find": {"out.s2":"1NsP5Sb9bSKR4D6vJT3h1as2Lq5YfphZYn","out.s5":"157957eb898913471dd77ca132e2d2e51f391e2e0e7c24fea9dec883372b073e"},
  }
}
```

Other endpoint can also be created to support requesting data.

### Reduce File Duplication

Anyone or upload service provider can query the data before uploading any file. If there is an onchain data exist, the uploading is not needed. They can simply use the txid of existing file, or just simply reference the file using its hash, without the need to upload.


## Conclusion

By using BP protocol, we can create a more secured eco-system as well as more efficent in using onchain data.
