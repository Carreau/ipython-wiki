# Introduction

The IPython spec allows for a very restricted set of messages, and particularly one for executing a string of code.  This requires that for any communication to change the state of the user namespace on the server, or to perform a computation and receive output, the client must form a valid python string of code.  This is error-prone and ignores the richer facilities we have for passing data back and forth between the client and the server.

Another way to look at it is: the IPython server has the ability to pass rich data (i.e., JSON) to the client, but the client can only pass strings back to the server to execute.  This IPEP provides a way for the client to pass JSON back to a custom message handler on the server.

## Discussion items

### Message types


message type namespaces/how to handle namespace clashes
* the signature for message handlers
* 