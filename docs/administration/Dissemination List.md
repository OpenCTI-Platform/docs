# Dissemination List

!!! tip "Enterprise edition"

    Dissemination List is under the "OpenCTI Enterprise Edition" license. Please read the below page to have all the information.

Dissemination list in OpenCTI aims to help you create mailing lists that are used to send (disseminate) finished intelligence. 


## Create a dissemination list
To create a dissemination list, you need to have the capability Manage credentials (see the list of [capabilities in the user page](https://docs.opencti.io/latest/administration/users/) for more information). 
Access the security menu & click on dissemination list.

![dissemination-list-overview.png](assets%2Fdissemination-list-overview.png)

Here you can view all the existing lists that are provisioned in the platform.


Click on create dissemination list: simply add a name & copy/past or write all the emails you want emails to be sent to. You need to have one email per line.

![dissemination-list-create.png](assets%2Fdissemination-list-create.png)


## Edit a list
Simply click on a list and edit the emails contained in the list


## Usage of dissemination lists
Dissemination lists are meant to be used in the context of Fintel Template.
First, you need to generate a finsihed intellignece document. If you need more details regarding how to configure a finished intelligence document, please look for more information on this [Fintel Template customization page](https://docs.opencti.io/latest/administration/entities/).
Once you have worked on your container & generated your template, whether it is an HTML document or a PDF document, if you have the capability to **Disseminate files by email**, you will be able to use your mailing list.

Click on the Disseminate button within the Content view of your container to access the configurations options. 

![Dissemination-list-send.png](assets%2FDissemination-list-send.png)


### Dissemination Configuration 

**Use OpenCTI template** 
By default enabled. This option allow you to decide whether or not you want to use the OpenCTI look and feel within your email, or if you would rather use an email without predefined style. 
This option is specifically useful if you would like to send the content of your finished intelligence document directly as an email body, without additionnal formatting. 

![dissemination-list-disseminate.png](assets%2Fdissemination-list-disseminate.png)


**Dissemination list**
Chose the dissemination list you want. Any dissemination list created within the platform will be shown in this dropdown.

**Email Subject**
Subject of the email.

**Use file content as email body**
By default disable. You can enable this option if you want the body of the email to be the content of your finished intelligence document. It only works with **HTML documents.**

**Email body**
Body of the email.

**File**
The file that you are sending by email. 

## Send an email using a dissemination list

### Recipients
All recipients of your email will be added as BCC, therefore, no data will be leak regarding who the email is sent to.
The user clicking on the button SEND will also be added in the list of the recipients, also as BCC, in order to ensure that the email is well received.

Depending on the size of the attachment or the email itself and your configuration, you could have to wait several minutes before being able to receive the email. 

### Email address used as sender
In Setting/parameters, you can define the **sender email address". This is the email that will be used to send the email to your list.







