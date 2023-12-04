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

Value --> admin
```

Click Apply

![40_save_params](images/40_save_params.png)

12. From the top grey Ribbon of the NiFi UI, select the first icon (processor) and drag it into the canvas

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

- Identify and select the fields that need to be anonymized.
- Use processors like `UpdateAttribute`, `ReplaceText`, or `AttributesToJSON` to manipulate the data.
- Apply masking techniques such as randomization, substitution, or encryption to the selected fields.
- Use processors like `EncryptContent` or `EvaluateJsonPath` to perform the required masking operations.
- Test the anonymized data to ensure that it meets the desired level of privacy and security.

_*For th purpose of this lab we have selected two processors ReplaceText and PutHDFS_

1. Repeat step `12` from the previous section **(2.1)**, filter for the ReplaceText processor, follow the configuration

![48_replace_text](images/48_replace_text.png)
![49_replace_text_rel](images/49_replace_text_rel.png)

2. Repeat step `12` from the previous section **(2.1)**, filter for the PutHDFS processor, follow the configuration. mMke sure to add passwords as they are set as Sensitive Values in the screenshot

![50_put_hdfs](images/50_put_hdfs.png)
![51_put_hdfs_rel](images/51_put_hdfs_rel.png)

3.  Repeat step `16` from the previous section **(2.1)**, connect both processors

![52_add_relationship](images/52_add_relationship.png)

4. For the purpose of this lab we will be creating an External Table in Hue. This table is the asset that end users will be accesing. Before that the permissions in Ranger need to be granted in the correct policy for the user admin

1. To access Ranger go to the EDGE2AI Home url: http://34.197.112.233/  and select Ranger

2. Login as Admin:

![27_ranger_login](images/27_ranger_login.png)

3. Go to cm_hdfs

![54_cm_hdfs](images/54_cm_hdfs.png)

4. Select policy 1

![55_select_policy_1](images/55_select_policy_1.png)

5. Update the valuues of the allow condition 


![56_update_policies](images/56_update_policies.png)


6. Go to Hue and run the following query in the Hive editor-->

![53_write_hue](images/53_write_hue.png)

```
CREATE DATABASE HR;

CREATE EXTERNAL TABLE HR.employees_Redacted
(id int,
name string,
age int,
phone string,
email string,
dateofbirth string,
region string,
salary int)

row format delimited 
fields terminated by ',' 
lines terminated by '\n' 
LOCATION '/user/admin/hive'
TBLPROPERTIES ("skip.header.line.count"="1");


SELECT * FROM HR.employees_Redacted;
```


### 2.3 Pseudonymize data in NiFi
To pseudonymize data in NiFi, there will be several options, including built-in processors, you can pick based on the strategy. For Data encryption, the "Encrypt Content" processor will support a number of very powerful and recognised encryption algorithms out of the box. For the purpose of this lab, we picked a hashing method, for illustration purposes, as hashing is not fully reversible and can be easily cracked.

Using ![Nifi Expression Language](https://nifi.apache.org/docs/nifi-docs/html/expression-language-guide.html) and a ReplaceText processor, the string is replaced with a hash before being written out into Hive.

The same regular expression is indicated to identify the email addresses in the source data.  
`([a-z]*)\.([a-z]*)@([a-z]*)\.([a-z.]{2,5})`

Using an expression from Nifi supported Expression Language, the last name in the email addresses is replace with a hash (Md5).
`${'$1'}.${'$2':hash('MD5')}@${'$3'}.${'$4'}`


![Pseudo_Processors](./images/54_pseudo_nifi processor.png)


The output is also written into HDFS to be read using Hue, in a different folder:  
`
![Pseudo_HDFS](./images/55_pseudo_HDFS.png)

The path picked here is:  
`/user/admin/hive/hashed`

In Hue, over the same database called "HR", run the below:  

```

CREATE EXTERNAL TABLE HR.employees_hashed
(id int,
name string,
age int,
phone string,
email string,
dateofbirth string,
region string,
salary int)

row format delimited 
fields terminated by ',' 
lines terminated by '\n' 
LOCATION '/user/admin/hive/hashed'
TBLPROPERTIES ("skip.header.line.count"="1");


SELECT * FROM HR.employees_hashed;
```


### 2.4 How to share Flows in NiFi

In NiFi user can create template that contain the flows that are in the canvas. At the same time, users can also import templates. For the purpose of this lab, there is a template un the `assets` directory in this repository

To upload a Nifi template:  
1. In the Nifi Canvas UI, select the "Upload template" button in the left hand side configuration menu 

![6_nifi_upload_template](images/6_nifi_upload_template.png)  

2. There will be warnings in each of the processors as the parameters will need to be configured

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













