# CSO Hands-on Lab

The primary goal of this repo is to cover the following-->

**1.** Using Nifi as a ingestion and transformation tool to:
  - Ingest data from a local repository into HDFS
  - Pre-process the files while covering different pseudonymization and anonimyzation techniques* 
    
  _*There will be a strong focus on reversible techniques (pseudonymization), by opposition to anonimyzation. Pseudonymisation is the process of replacing identifying information with random codes, which can be linked back to the original person with extra information; whereas anonymisation is the irreversible process of rendering personal data non-personal, and not subject to the GDPR_

  - Create separate tables for raw data and processed data

**2.** Create policies for different users in Ranger to provide the correct permissions for each of the data resources that are available from the previous step by giving access to users, profiling and masking data

**3.** Using Ranger and different CDP tools such as HUE (sql workbench),HDFS, and Jupyter Notebook; demonstrate how end users (analysts, admins and auditors) have access ONLY to the data that they are allowed to see/use

All steps will be run by attendees on their own edge to ai instance, a single node secured cluster running CDP
Private Cloud base with Nifi deployed. Because it's a very small instance, the purpose is to test features,
not to test performance of workloads at scale.

## 1. Access the EDGE2AI instance
To acess the instance, each user will need to register using the given Web Server and registration code. 
```
  Web Server:  http://34.197.112.233 
  Registration code:*clever_mccarthy
```

![1_registration](images/1_registration.png)

Once this is completed, register the user and create a password

![2_password](images/2_password.png)

You will be redirected to the main menu
_*Note: Users must check with their internal security team if the range for the services has to be whitelisted_

![3_main_menu](images/3_main_menu.png)

There can be different users that have access to the CDP platform, however for the purposes of this lab we will be using one:

**Admin**

```
  User: admin
  Password: Supersecret1
```


## 2. Data Ingestion and Transformation with NiFi
To access the NiFi UI go to the EDGE2AI Home url: http://34.197.112.233/  and select NiFi

Login as admin:

![4_nifi_login](images/4_nifi_login.png)


You will be redirected to the NiFi Canvas -->

![5_nifi_canvas](images/5_nifi_canvas.png)


### 2.1 Ingesting Data using NiFi

Pipelines in NiFi are referred as flows. 
The first step of the Flow that is going to be created will be ingesting the data from a storage system in this case HDFS.

For the purpose of this lab, there is a pre-step where users will download a sample dataset from this Repository:

```
  https://github.com/nhernandezdlm/CSO_HoL/tree/main/assets
```
The dataset will be uploaded into the local HDFS of each of the clusters using Hue.

To access Hue go to the EDGE2AI Home url: http://34.197.112.233/  and select Hue

Login as Admin:

![7_hue](images/7_hue.png)

You will be redirected to Hue -->

![8_hue_init](images/8_hue_init.png)

Follow these steps:

1. Go to Files

![9_files_hue](images/9_files_hue.png)

2. Create a New directory

![10_hue_directory](images/10_hue_directory.png)

Name it:

```
  test_data
```

![11_directory_name](images/11_directory_name.png)

3. Navigate to the directory

![15_directory](images/15_directory.png)

4. Upload the file

![12_hue_upload](images/12_hue_upload.png)

![13_hue_upload_2](images/13_hue_upload_2.png)

5. The file is stored in HDFS

![14_hdfs_file](images/14_hdfs_file.png)

6. Go back to the NiFi Canvas. Before adding any processor, the parameters must be configured. These are created within the Parameter Contexts. These contexts allow users to bind a set of configurations to any connector pipe at runtime. For that, click on the Hamburger menu on the top right of the canvas and select "Parameter Contexts"

![16_Nifi_New_Parameter_Contexts](images/16_Nifi_New_Parameter_Contexts.png)

7. Click on the + sign on the top right

![17_NiFi_Create_new_parameter_context](images/17_NiFi_Create_new_parameter_context.png)

8. Add the Name `root` and click on Apply

![18_Root_Parameter_context](images/18_Root_Parameter_context.png)

9. Close the window and do right click on any place in the canvas, click on Configure

![19_Set_ParameterContext](images/19_Set_ParameterContext.png)

10. Select root from the dropdown and click on Apply

![20_a_select_root](images/20_a_select_root.png)

and click on Apply 

![20_Apply_Parameter_Context](images/20_Apply_Parameter_Context.png)

11. Go back to the Paramenter Context on the Hamburger menu, and click on the pencil on the right side of the root row

![37_param_context](images/37_param_context.png)

12. Click on the + sign on the top right and add the information for the following parameters: 

![38_add_param](images/38_add_param.png)

```
Name --> Hadoop Configuration Resources

Value --> /etc/hadoop/conf.cloudera.hdfs/core-site.xml,/etc/hadoop/conf.cloudera.hdfs/hdfs-site.xml,/etc/hadoop/conf.cloudera.hdfs/ssl-client.xml,/etc/hive/conf.cloudera.hive/hive-site.xml
```

```
Name --> Kerberos Password

Value --> <admin-password>
```
_* For the password select Sensitive Value YES_


![39_add_info_param](images/39_add_info_param.png)

```
Name --> Kerberos Principal

Value --> <admin>
```

Click Apply

![40_save_params](images/40_save_params.png)

12. From the top gret Ribbon of the NiFi UI, select the first icon (processor) and drag it into the canvas

![41_add_processor](images/41_add_processor.png)

13. Type in the textbox `ListHDFS`

![42_processor](images/42_processor.png)

14. Configure the Properties Tab

![43_configure_list_hdfs](images/43_configure_list_hdfs.png)

_*For the enviroment variables add a # + { and then press ctrl + space --> this will show the options that have been configured in the Parameter Contexts_

![43a_tab_for_params](images/43a_tab_for_params.png)

15. Repeat step `12`, filter in the textbox for `FetchHDFS` and configure both the Properties and Relationships tabs

![44_configure_fetch_hdfs](images/44_configure_fetch_hdfs.png)

![45_config_relationship_fetch](images/45_config_relationship_fetch.png)

16. Click on the dark blue icon with an arror inside of the processor and drag it to the other processor. Click on add

![46_connect_arrow](images/46_connect_arrow.png)

![47_add_relationship](images/47_add_relationship.png)


### 2.2 Anonymize data in NiFi

To anonymize data in NiFi, you can make use of various processors and techniques. Here are some common steps to follow:

1. Identify and select the fields that need to be anonymized.
2. Use processors like `UpdateAttribute`, `ReplaceText`, or `AttributesToJSON` to manipulate the data.
3. Apply masking techniques such as randomization, substitution, or encryption to the selected fields.
4. Use processors like `EncryptContent` or `EvaluateJsonPath` to perform the required masking operations.
5. Test the anonymized data to ensure that it meets the desired level of privacy and security.


### 2.3 Pseudonymize data in NiFi

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

### 2.4 How to share Flows in NiFi

The Nifi template is available under "assets" in this repository if needed.  

Upload a Nifi template:  
in the Nifi Canvas UI, select the "Upload template" button in the left hand side configuration menu.!
![6_nifi_upload_template](images/6_nifi_upload_template.png)  



## 3. Data Masking in Ranger and Atlas

Withing the Cloudera tech stack, Atlas provides open metadata management and governance capabilities for organizations to build a catalog of their data assets, classify and govern these assets. Within Atlas, users can create their own classifications.

For example, resources (HDFS file/directory, Hive database/table/column etc.) containing sensitive data such as social security numbers,credit card numbers, or sensitive health care data can be tagged with any classification tag of the user's choice (usually "PII" or "confidential" or "sensitive") in Atlas, either as the resource enters the Hadoop ecosystem or at a later time. 

On the other side, Ranger enables users to create tag-based services and add access policies to those services.

*Tag-Based Policies Overview*
An important feature of Ranger tag-based authorization is the separation of resource-classification from access-authorization. 

Once a resource is tagged in Atlas, the authorization for the tag would be automatically enforced with Ranger,
thus eliminating the need to create or update policies for a particular esource.

Using tag-based policies also enables users to control access to resources across multiple CDP components without creating separate services and policies in each component.


Steps to follow --> 

### 3.1 Access Atlas and assign a "PII" categorization to Hive column "email" from the Hive table "employees"

To access Atlas go to the EDGE2AI Home url: http://34.197.112.233/  and select Atlas

1. Login as Admin:

![21_atlas_login](images/21_atlas_login.png)

2. In the Search By Text textbox type employees to find the table that has been created

![22_atlas_search](images/22_atlas_search.png)

3. Click on the table and explore the different tabs, as you can see Atlas inferred the metadata. Go to the schema tab

![23_atlas_table_schema](images/23_atlas_table_schema.png)

4. As part of the exercise the assumption is that the `email` column needs to be masked. For that, we will create a classification. For that click on the Classification option on the left blue panel and click on add. Add PII in the name and click Create

![24_create_classification](images/24_create_classification.png)

5. Once the classification is created go back to the table, click on the + icon in the right side of the `email` row and select PII

![25_add_classification](images/25_add_classification.png)

The entity `email` has been classified as PII
![26_column_class](images/26_column_class.png)

### 3.2 Access Ranger and create a Masking policy to redact any asset tagged PII


To access Ranger go to the EDGE2AI Home url: http://34.197.112.233/  and select Ranger

1. Login as Admin:

![27_ranger_login](images/27_ranger_login.png)

2. Within the Ranger UI, click on the Tag tab

![28_ranger_ui](images/28_ranger_ui.png)

3. Click on cm_tag and then go to the Masking Tab

![29_select_tag](images/29_select_tag.png)

4. Click on add New Policy

![30_masking](images/30_masking.png)

5. Name the policy, select the PII Tag, select the component permissions, masking options and create new policy

![31_pii](images/31_pii.png)

![32_hive](images/32_hive.png)

![33_masking_option](images/33_masking_option.png)

_*For this lab, Redact has been selected as the masking option_

![34_create_policy](images/34_create_policy.png)


### 3.3 Query the table from Hue and have a look at the result

1. Go back to Hue

2. Run the query by going to the editor icon and selecting Hive


![36_hue_editor](images/36_hue_editor.png)


```
SELECT * FROM HR.employees;
```

3. Review the results 

![35_hue_mask_results](images/35_hue_mask_results.png)













