Edge to AI deployment:
For admins, to set up the Lab, deploy as many edge to ai instance as required for the number of attendees.
Use the Stack 719 secured and double check you have Nifi and Ranger in the service list.
A web server should be deployed by default, to help grant access to each of the attendees.


Test data:
-HR Employee file available in the demo tenant.




Access and permissions:
Create a "PII" classification in Atlas
For Masking:
- Make sure the users have access to assign tags to entities in Atlas
- Make sure the users have access in Ranger to create Tag Based policies (its not a given for Read-Only users)
