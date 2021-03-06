# Example Code: Request-Reply DynamicData Application

## Concept

*Dynamic Data* API allows to create topic samples in a programmatically manner
without defining an IDL in compile time.

*Dynamic Data* provides an easy way to access the members of a data sample. Such
members can be primitive data types or complex data types such as structs,
arrays or sequences.

While the access to certain members is done using a name, the access in multiple
cardinality data types (such as arrays or sequences) are done using a index
based access.

On the other hand, as applications become more complex, it often becomes
necessary to use other communication patterns in addition to publish-subscribe.

Connext DDS includes support for the *Request-Reply* communication pattern. 
It is available with the Connext DDS Professional, Evaluation, and Basic package
types.


The request-reply pattern has two roles: 
* The requester (service consumer or client) sends a request message and waits
for a reply message.
* The replier (service provider) receives the request message and responds with
a reply message.

## Example description

This example shows how to establish a Request-Reply communication pattern using
the Request-Reply API based on Dynamic Data types.

Within this example the Requester application requests a list of prime numbers
from 1 to `n`. Then the Replier application computes the prime numbers and
replies with the given sequence of prime numbers to the Requester.

For this example we dinamicaly define two data types, one for the request 
samples and the other for the reply samples. The request data type is the
following:

```cpp
struct PrimeNumberRequest {
    //Requests the calculation of the prime numbers below n 
    long n;
    //How many prime numbers should be included in each reply sample. 
    long primes_per_reply;
```

And then the reply data type:
```cpp
//Maximum size of the a sequence of prime numbers
const long PRIME_SEQUENCE_MAX_LENGTH = 1024;
//Indicates the status of one of the multiple possible replies for a request
enum PrimeNumberCalculationStatus {
    //Indicates that this reply contains a new sequence of
    //prime numbers for a request, but there are still more to come
    REPLY_IN_PROGRESS,
    //Indicates that this is the last sequence of 
    //prime numbers for a request.
    REPLY_COMPLETED,
    //Indicates that there was an error. After an error
    //there won't be any more replies for a request
    REPLY_ERROR
};


// The replier sends sequences of prime numbers as they are being calculated.
struct PrimeNumberReply {
    // A sequence of prime numbers
    sequence<long, PRIME_SEQUENCE_MAX_LENGTH> primes;    
    // Status information about this reply
    PrimeNumberCalculationStatus status;
};
```

All the Reply/Request communication is done using the previously mentioned
Dynamic Data data types that are defined in "_PrimesType.cxx_".
