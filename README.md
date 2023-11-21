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

Summary
1. Identify and select the fields that need to be anonymized.
2. Use processors like `UpdateAttribute`, `ReplaceText`, or `AttributesToJSON` to manipulate the data.
3. Apply masking techniques such as randomization, substitution, or encryption to the selected fields.
4. Use processors like `EncryptContent` or `EvaluateJsonPath` to perform the required masking operations.
5. Test the anonymized data to ensure that it meets the desired level of privacy and security.

The Nifi template is available under "assets" in this repository if needed.

















_*Pseudonymisation is the process of replacing identifying information with random codes, 
which can be linked back to the original person with extra information, 
whereas anonymisation is the irreversible process of rendering personal data non-personal, 
and not subject to the GDPR_
