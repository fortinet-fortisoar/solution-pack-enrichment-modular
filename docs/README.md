# Modular Enrichment Playbooks
This collection is dedicated to a modular approach to enrichment playbooks. A primary enrichment playbook is responsible for all indicator types enrichment.
For each type several referenced playbooks are used to perform the enrichment from a single cyber threat intelligence source.
The primary playbook collects enrichment data from all references playbooks and computes the indicator attributes.

# Referenced Playbooks (Modules)
At least one referenced playbook is required per indicator type. the playbook only function is to fetch threat intelligence data from one Cyber Threat Intelligence (CTI) provider and compile the results as described in the return values sections below.
If you want to contribute a reference playbook for a new CTI you should follow the below specifications:

## Parameter:
The playbook has only one input parameter :**artifact** which is a the artifact value to rate

## Enrichment TAG:
- Indicator type Tag: **[INDICATOR TYPE]_Enrichment**, the available tags would be:
    - IP_Enrichment
    - URL_Enrichment
    - Domain_Enrichment
    - Email_Enrichment
    - Hashcode_Enrichment (which includes md5/sha1/sha256)
    - Host_Enrichment
    - Domain_Enrichment  
    - File_Enrichment  
    - Port_Enrichment  
    - Registry_Enrichment  
    - Process_Enrichment

## Return Values
The playbook must have the last step as **SET VARIABLE** with the below variables defined:
**cti_name**: The CTI source, exp: FortiGuard
**cti_score**: Integer representing the artifact severity (maliciousness) preferably between 0 and 10 to compute the mean more accurately if that's needed.
**ioc_attributes**: is a JSON dictionary with all the attributes you want to use for enrichment, the syntax is as follows:
```json
{"Indicator_attribute1":"Enrichment Value1","Indicator_attribute2":"Enrichment Value2"...etc}
```
so for example if the attribute country is to be populated from the current referenced playbook the syntax would be:

```json
{"country":"Canada"}
```
The attribute name must be exactly as defined in FortiSOAR module, you can check it by browsing to *Settings > Modules* Select Indicator then the field you are looking for. the field name would be as defined by *Field API Key*

Example: 

![](garh.png)

**enrichment_data**: a JSON object with the list of tables holding the threat intel details as per the below example:

```json
[
    [
        "Table 1 Title",
        {
            "key1": "value1",
            "key1": "value1",
            "key1": "value1",
            ...etc                
        }
    ],
    [
        "Table 2 Title",
        {
            "key1": "value1",
            "key1": "value1",
            "key1": "value1",
            ...etc                
        }
    ]
    ...etc
]
```


in summary, to add a referenced enrichment playbook for any indicator type, it would be enough to return the 3 variable above and adding the appropriate tag to the playbook then reset the list of available playbooks with the playbook **Update Indicator Enrichment Global VARs**

# Update Indicator Enrichment Global VARs Playbook:

Each time a new referenced playbook is added or removed this playbook has to be executed to update the list of available enrichment playbooks for each indicator type. It does it by reading the IRIs of all enrichment playbooks by type, then store each list in a global variable.
The result would be a global variable such as:
- **IP_Enrichment_Playbooks_IRIs** with the value:
    - /api/3/workflows/30c2465a-29a6-424b-a3ff-8080d269f422,/api/3/workflows/6a90a0b7-5b49-4774-ae2d-455409b77ea5,/api/3/workflows/9da329ef-7b8e-44ec-aed1-1cd41f9bd1a8,/api/3/workflows/a6a92cc5-1934-41c6-a608-8510d38c4dbc

# Primary Playbook: 

The playbook **Indicators Enrichment - Modular** runs with the OnCreate trigger for each indicator. It fetches the list of available referenced playbooks for the current indicator type and runs them in a loop to get the threat intel data from each. The data is then compiled to determine the indicator reputation, description and all the fields defined by the **ioc_attributes** variables from each executed referenced playbook.


# Currently Supported Indicators Types:

### IP Address

![](ip_indicator.png)

### URL

![](url_indicator.png)

### Hashcode (md5/sha1/sha256)

![](hash_indicator.png)

### Domain

![](domain_indicator.png)

### Email

![](email_indicator.png)

# Deployment:

- If you have the default IR content pack, you will need to disable the equivalent playbooks:
    - Indicator (Type Domain) - Get Reputation
    - Indicator (Type MD/SHA1/SHA256) - Get Reputation
    - Indicator (Type IP) - Get Reputation
    - Indicator (Type URL) - Get Reputation
    - Indicator (Type Email) - Get Reputation
- Download the code zip from here (code then Download Zip file)
- Import the playbook collection via the import wizard. Browse to: **Settings -> Import Wizard** and click on **Import from file** selecting the zip file you just downloaded
- Activate the playbooks provided in this content pack (by default all playbooks in this collection are inactive)
