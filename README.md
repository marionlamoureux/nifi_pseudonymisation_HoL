Nifi Pseudonymisation - Hands-on Lab

The purpose of the lab is to cover various pseudonymisation technics available in CDP private Cloud and Nifi.
We'll focus on reversible techniques, by opposition to anonimyzation that is not reversible.*

Summary:
- Use Nifi processor
- Using Ranger for profiling and masking

All steps will be run by attendees on their own edge to ai instance, a single node secured cluster running CDP
Private Cloud base with Nifi deployed.

# 1. Access your edge to ai instance
You can ssh to it if needed using the hostname indicated in the webserver.


# 2. Pseudonymisation in Nifi
Access Nifi UI and the canvas
To anonymize data in NiFi, you can make use of various processors and techniques. Here are some common steps to follow:

Anonimisation:
Summary
1. Identify and select the fields that need to be anonymized.
2. Use processors like `UpdateAttribute`, `ReplaceText`, or `AttributesToJSON` to manipulate the data.
3. Apply masking techniques such as randomization, substitution, or encryption to the selected fields.
4. Use processors like `EncryptContent` or `EvaluateJsonPath` to perform the required masking operations.
5. Test the anonymized data to ensure that it meets the desired level of privacy and security.

The Nifi template is available under "assets" in this repository if needed.


Pseudonymisation
To pseudonymize data in NiFi, you can make use of the built-in Record processors like ConvertRecord and UpdateRecord. These processors allow you 
to manipulate the data within a record-oriented format and apply transformations to the data fields.

Here are the steps to pseudonymize data using NiFi:

1. Use a processor like ExtractText or SplitText to split the incoming data into individual fields, if applicable.

2. Configure the ConvertRecord processor to specify the desired Record Reader and Record Writer. These can be set to appropriate JSON, Avro, or CSV reader/writer controller services.

3. Set up a schema for the data to define the fields to be pseudonymized. You can create a JSON schema or use Avro schema depending on the chosen format.

4. Configure the ConvertRecord processor to apply conversions to the fields for pseudonymization. You can utilize the built-in Record processors like UpdateRecord or write your custom scripts in a scripting language like Groovy, Jython, or JavaScript.

5. Apply the pseudonymization logic to the desired fields in the UpdateRecord processor or any custom scripting processor. These transformations could include functions to hash, encrypt, or replace the original values with pseudonyms.

6. Configure the ConvertRecord processor to specify the desired Record Writer to write the pseudonymized records.

7. Connect the ConvertRecord processor to the downstream processors or destination systems to further process or store the pseudonymized data.

It's important to note that while NiFi provides the tools to pseudonymize data, 
the actual pseudonymization algorithms or techniques need to be implemented within the custom scripting or transformation logic used in the processors.

If you have additional requirements or complex pseudonymization techniques, 
you might need to consider implementing a custom processor or leveraging external services for more advanced data anonymization.

Let me know if you require further assistance or if Cloudera has trained me on this specific use case.














_*Pseudonymisation is the process of replacing identifying information with random codes, 
which can be linked back to the original person with extra information, 
whereas anonymisation is the irreversible process of rendering personal data non-personal, 
and not subject to the GDPR_
