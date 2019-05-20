# CPAS Workshop
**NOTE: This Repository contains all relevant exercises for the CPAS Workshop**

### Disclaimer
The following is intended to outline our general product direction. It is intended for information purposes only, and may not be incorporated into any contract. It is not a commitment to deliver any material, code, or functionality, and should not be relied upon in making purchasing decisions. The development, release, and timing of any features or functionality described for Oracle’s products remains at the sole discretion of Oracle.

# Table of Contents
* [Exercise 1: Creating Custom Parsers & Dashboards](https://github.com/dbrett90/CPASWorkshop#exercise-1-creating-custom-parsers)
* [Exercise 2: Visualizations & Custom Dashboards](https://github.com/dbrett90/CPASWorkshop#exercise-2-visualizations--custom-dashboards)
* [Exercise 3: Creating Groups](https://github.com/dbrett90/CPASWorkshop#exercise-3-creating-groups)
* [Exercise 4: Mock Security Monitoring & Analytics](https://github.com/dbrett90/CPASWorkshop#exercise-4-mock-security-monitoring--analytics)
* [Exercise 5: Creating Alert Rules](https://github.com/dbrett90/CPASWorkshop#exercise-5-creating-alert-rules)
* [Useful Links](https://github.com/dbrett90/CPASWorkshop#useful-links)
## Exercise 1: Creating Custom Parsers
### Creating Custom Fields
Open your Oracle Management Cloud Console and navigate to Administration > Log Admin > Fields. We will be creating a log parser for the data, but first need to specify a few custom fields contained within the Web Application Firewall log files that do not come prepackaged within OMC.
![1.)](/imgs/pic17.png?raw=true)  

If you open up the WAF Log file (in texteditor, **NOT** in Excel) you will see all relevant fields and each delimited by a comma. While some of these fields are included out of the box (e.g. Country Code & Continent Code) there are a number which are not contained natively within OMC. These custom fields  include WAF Alerts, Request URL, Referrer, Response Code, Response Size & Fingerprint among others. We will need to make custom fields for each of these (you can check to see if a field already exists by utilizing the search bar). To create a custom field simply click the create button, name the field and add the type (typically string). A description is optional. Click '"Save." You will repeat the process of creating a custom field for each one **before** you create the custom parser.
![1.)](/imgs/pic18.png?raw=true)

### Creating the Custom Parser
Once you have created all necessary custom fields, click the log parsers tab. Select "Create". Name the parser and paste one line from the log file (from text editor, **NOT** Excel as Excel won't copy as comma-delimited) into the example log content block. Select "Next". Highlight the entire block and click "Next."
![1.)](/imgs/pic19.png?raw=true)

Highlight each field and match with the corresponding field. Make sure that each field passes the parser test (will highlight green if successful). For timestamp make sure this is in POSIX format. Click next when done and create the parser.
![1.)](/imgs/pic20.png?raw=true)

### Creating the Custom Log Source
In the OMC Console go to Log Sources > Create.
![1.)](/imgs/pic21.png?raw=true)

Name the parser and leave the source type as "file." Select "Cloud Agent" as entity type and the name of the parser you created under file parser. For included patterns if you have the WAF Logs on your linux box (could use a ```scp``` command) you will need to give the parser the filepath (EX: ```/home/opc/wafLogs/*.csv```) and either name the file or give it a generic ```*.csv``` extension. If you have these logs stored on your local host the next lab will explain how to upload those files directly into OMC. Make sure you click save to enable the log Source.
![1.)](/imgs/pic22.png?raw=true)

## Exercise 2: Visualizations & Custom Dashboards
### Uploading Data
If you have not securely copied the WAF log file to your compute node, you can manually upload it into OMC. Navigate to `Log Admin` > `Uploads` > `New Upload`.
![1.)](/imgs/pic23.png?raw=true)

Name the Upload, select the files from your browser and click the `>` button. Now make sure to associate the upload with the log source you just created and the entity with your cloud agent. Click the > button. Review and Upload.
![1.)](/imgs/pic24.png?raw=true)

On the uploads page click the hamburger menu next to your upload and select "View in Log Explorer."
![1.)](/imgs/pic25.png?raw=true)

If the log parser was correctly configured you should be able to click into a given record and see the information in human readable form. If done incorrectly there will be a blue "i" next to the entry.
![1.)](/imgs/pic26.png?raw=true)

### Creating Visualizations
We can now start creating visualizations. Change the graph on the visualization option and drag the field (from the other dropdown) into the "Group By" box. Click "Save > Save As."
![1.)](/imgs/pic27.png?raw=true)

Name the graphic, select "Add to Dashboard" and choose the "New Dashboard" option. Name the Dashboard and click save. Continue making and adding graphics to this dashboard until you are satisfied with visuals.
![1.)](/imgs/pic28.png?raw=true)

A quick deep dive on visualizations- options within Log Explorer include:
1. Pie Charts
2. Line Graph
3. Histograms
4. Bar Graphs
5. Word Maps
6. Treemaps
7. Global Activity Map

Log Analytics can also leverage Oracle's proprietary AI & ML Algorithms to create more insightful and targeted visuals. There are two options that you can select - either **clustering** or **linking**. 
Clustering uses machine learning to identify the pattern of log records, and then to group the logs that have a similar pattern. Clustering also  helps significantly reduce the total number of log entriesthat you have to explore and easily points out the outliers. Linking  lets you perform advanced analysis of log records by combining individual log records from across log sources into groups, based on the fields you’ve selected for linking. You can analyze the groups by using the same fields as the ones you used for linking or additional fields for observing unusual patterns to detect anomalies.
Link command can be used for a variety of use-cases. For example, individual log records from business applications can be linked to synthesize business transactions.
Groups can also be used to synthesize user sessions from web access logs. Once these linked records have been generated, they can be analyzed for anomalous behavior.

When creating your custom dashboards, try to use at least one Clustering or Linking visualization.

## Exercise 3: Creating Groups

### Overview
Groups are an efficient way to logically organize, manage, and monitor the entities in Oracle Management Cloud. Creating a group enables you to collectively monitor and administer multiple entities as a single logical unit. For example, you can define a group to contain all the WebLogic Servers in a production cluster. A group can include entities of the same type, such as all production databases, or it could include multiple types of entities.

There are two types of groups: **Static** & **Dynamic**. A static group is exactly like it sounds - a manually defined clustering of associated entities. A static group can be created within the OMC console and should be created if membership in the group is unlikely to change. A dynamic group allows you to add entities to a group based on set membership criteria. In dynamic environments where new entities come into the system frequently, entities that match the membership criteria are added automatically to a dynamic group. These groups must be created via REST API, but can be viewed within the OMC Console.

### Creating a Static Group
Navigate to ```Administration > Entity Configuration > Groups. ``` Select "New Group."
![1.)](/imgs/group1.png?raw=true)

Because we are operating within the console, the group will be defined as static (Future releases of OMC will allow for dynamic groups to be defined within the UI as well). Name the group, click "Add" and select all entities that you want in the group. Save the group.
![1.)](/imgs/group2.png?raw=true)

Now that the static group has been created you can now investigate logs for a specific grouping. Navigate to your log explorer and filter by the groupname you just created. You now have access to all log records for that specific grouping.
![1.)](/imgs/group3.png?raw=true)

### Creating a Dynamic Group

For now, Oracle Management Cloud REST API documentation access is limited to approved customers. Contact your Oracle Support or Sales Representative for more information about accessing and using the Oracle Management Cloud REST API documentation. As a result we will not be able to walk through creating a dynamic group in this workshop, but would be happy to walk through this at a later date.

## Exercise 4: Mock Security Monitoring & Analytics
Security Monitoring and Analytics can be configured for any given piece of Cloud Infrastructure, but this will also require specific configuration of identity context. To speed the process up we will upload our own custom log files to demonstrate a number of specific threats that will also showcase identity context.

First, Navigate to `Menu Icon` > `Home` > `Administration` > `Entities Configuration` > `Licensing`. Click on each licensing configuration to match the below screenshot (i.e. Log Collection = Enabled, SMA Enrichment = Enabled, Assignment = Enterprise + Config & Compliance)
![1.)](/imgs/pic31.png?raw=true)

ssh into your linux box. Create the Create the directory `/u01/stage`. Copy the zipped [SMA data](/assets/sma-sample.zip) to `/u01/stage` and unzip the file.

In addition to log data, sample user context data will be added to OMC as well to provide a richer experience by mapping users identified in security logs to detailed corporate directory sourced from the company’s Identity Service. In real production setting, this data will come from an Identity Service such as IDCS and by means of integration.

In your linux host session, navigate to /u01/stage
1.      From [My Cloud Services Dashboard](https://myservices.us2.oraclecloud.com/mycloud/cloudportal/dashboard), toggle "Identity Domain" selector to your "Traditional" domain, then click on `Management Cloud` as shown below
 ![1.)](/imgs/pic32.png?raw=true)

1.      Get the Tenant ID, OMC Service Instance Name, and OMC Username as shown below
![1.)](/imgs/pic33.png?raw=true)

1.      Using the items gathered above, update environment variables accordingly as shown in this example
```
export tenantID=acmeinc                 #Tenant ID
export instanceName=ops                 #OMC Service Instance Name
export username=John.Doe@gmail.com      #OMC Username

```
3.      Execute the Unix shell script `omc_upload_usr_context.sh` and type in your OMC account password when prompted to upload User context file

```
[cdsma@myhost]$ ./omc_upload_usr_context.sh
```

For the benefit of this lab, you will be uploading sample Linux Secure logs files to OMC covering the following 3 security threats types:

   - `Target Account Attack `
   - `Multiple Failed Login `
   - `Brute Force Attack `

In real production setting, this data will be continuously streamed to OMC by OMC cloud agents and API integration. SMA supports uploading user data available in SCIM or LDIF format. The former will be used.

1.      From the same Linux host as indicated earlier and still with the environment variables set, run the Unix shell script `omc_upload_security_events.sh` to upload Linux secure logs.

```
[cdsma@myhost]$ ./omc_upload_security_events.sh
```


Once you have successfully uploaded the three log files, navigate back to the Uploads page. Refresh the page to see your uploaded log files. If you go to the Security Monitoring & Analytics page you should now be able to see potential threats.

## Exercise 5: Creating Alert Rules

### Overview
Oracle Management Cloud (OMC) collects performance and availability metrics for all entities set up for monitoring. The alerts sub-system informs you of availability or performance problems. Alert Rules enable you to define how alerts are triggered (if they are not automatic) and how you get notified.

### Create a Notification Channel
When creating an alert rule, we first need to define how we will be alerted if the defined threshold is exceeded. OMC supports Email, Mobile, Webhook, Page Duty, Servicenow & Slack Integrations out of the box. To create a Notiication channel navigate to ```Administration > Notification Channels > Create Notification Channel```.
![1.)](/imgs/notif_channel.png?raw=true)

Select the method by which you want to be notified, name the channel and add the necessary fields (e.g. email addresses, OMC user names, slack users, etc). Select "Create" when done.
![1.)](/imgs/notif_channel2.png?raw=true)

### Defining the Rule
Navigate to ```Administration > Alert Rules``` Select the Service for Which the alert rule will be created for. Select "Create Alert Rule."
![1.)](/imgs/alert_rule1.png?raw=true)

Name the rule and select whether it should be captured in real time or for an interval. If scheduled for an interval this typically means that the system is looking for a number of events to occur within the scheduled period. Choose the rule itself, whether it's a fixed threshold or anamalous activity & the relevant notification channels. Save the rule.
![1.)](/imgs/exAlert.png?raw=true)

Depending on the rule & service, you should start to see a number of notifications populate within your dashboards for the assigned alerts.

## Useful Links

All links below are directly from Oracle's Official Documentation. 
* [Creating Custom Dashboards](https://docs.oracle.com/en/cloud/paas/management-cloud/gfadg/work-dashboards.html#GUID-DEF59FAF-9FDF-49F7-8B72-ACF9FA72C552)
* [Deploying Cloud Agents for IM](https://docs.oracle.com/en/cloud/paas/management-cloud/emaig/generic-prerequisites-deploying-oracle-management-cloud-agents.html)
* [APM Information & Deployment](https://docs.oracle.com/en/cloud/paas/management-cloud/dmapm/introduction-application-performance-monitoring.html#GUID-956CE4CE-79EA-4088-B088-178168B0D20F)
* [Security Monitoring & Analytics](https://docs.oracle.com/en/cloud/paas/management-cloud/omsma/getting-started-oracle-security-monitoring-and-analytics.html#GUID-C1CE163B-3ECC-429D-9AA6-C00B9DECBF54)     
