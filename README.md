# API design exercise
Design a RESTful API using RAML that contains a single resource, customer, and allows the following:

- List customers
- Create a new customer
- Update a customer
- Deletes a customer

You may constrain the customer object to first name, last name and addresses, and the format to JSON. 

The API must be designed to support the following use cases at a minimum:

A periodic (every 5 minutes) synchronisation of changed customers from the API to some target system
A mobile application that allows customer service representatives to retrieve and update the customers details
Simple extension of the API to support future resources such as orders and products
The deliverable will be assessed against the following:

Best practices for RESTful API design
Use of built in aspects of the HTTP protocol (status codes, headers etc)
Use of RAML features
Efficient use of the network (especially for use case 2)
Applicability of the API to the use cases (including edge and exceptional scenarios)
Breadth of considerations in the design
Please deliver the following:

The RAML spec itself
Commentary on the interaction of use case 1 with the API
Commentary on interaction of use case 2 with the API
Commentary on how the API could be extended for use case 3
Commentary on any 'interesting' design decisions you made (and alternative options considered)

# Possible Solution
## Use Case 1
Synchronisation can be achieved by a client listing all customers with a query parameter containing the time of the last sync. Every object returned will contain the full contents so should the timestamp from the last update overlap this will not cause duplicates.

An alternative approach could be the use of a transaction history to give more detail about the changes that have occurred to the customer object over time. I assumed that past history of the customer object doesn't need to be retained by the target system thus the simpler approach of storing and using the last updated timestamp should be sufficient.

Additional context types could be supported to help transferring bulk data such as CSV.

Another alternative would be to use an async callback to send the data as it changed or periodicly. This adds addidtional complexity on the server side in falure scenarios but adds the flexibility of keeping the synced data upto date more regularly.

## Use Case 2

The 201 HTTP status code doesn't need to return the object back to the client. I would expect the REST Service to return the HTTP Header "Location" containing the URL to the resource giving the client the option of retrieving the stored object or not.

I was unsure how to represent this in RAML or if it was even possible so used a description to describe this intent.

## Use Case 3

Products and orders can be introduced as new resources at the top level as well as the addition of sub-resources under the customer resource indicating which orders and products are associated with a given customer.

## Other Considerations
I spent a bit of time first reading about RAML and the differences with 0.8 and 1.0. After creating a RAML 1.0 file to take advantage of types I struggled to find something that could parse it successfully so I fell back to RAML 0.8. It would be nice to spend some time implementing the service from the RAML file to ensure that it is actually correct and functions the way I intend.

In addition I used SSL and BasicAuth in the hope that I could actually implement something quickly without the added complexity of tokens or other authentication quirks.

For create and delete requests I didn't take into account all possible error scenarios however did consider defining a JSON schema for returning an error in the body as well as utilising HTTP error codes.

Finally, I used a string type for the date field in the queryParameter as I found that RAML 0.8 date field type was RFC2616 not ISO 8601 which is used in the JSON schema. This was changed in RAML 1.0 to ISO 8601 but in order to support the ISO format I used a string type. This may be a poor decision depending on how clients read and use the RAML file. I would investigate this incompatibility further before implementing a service.
