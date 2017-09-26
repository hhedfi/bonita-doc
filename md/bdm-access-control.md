# BDM access control - documentation

::: info
**Note:** for Performance and Efficiency editions only.
:::

## Overview

## Use of profiles

## Define BDM access control

## Example of use-cases to implement

### Human resources novice

**Use case:** In a company, there is two kind of human resources employee: The experimented ones and the novice ones. Human resources employees have to review other employees leave requests. Leave requests often include a medical comment. *A novice human resources employee shall not see the medical comment of a leave request.*

**Technical use case:** Grant access to some attributes of a business object depending on the profiles of the requester.

::: info
**Note:** Part III and IV are not directly related to the access control definition, it is just a convenient way to observe the result of the access control definition.
:::

**I - BDM definition**

First, define a *LeaveRequest* business object, with the following attributes:  

 | Name | Type |
|---|---|
| employee | STRING |
| departureDate | DATE ONLY |
| duration | INTEGER |
| medicalComment | STRING |
| managerComment | STRING |

**II - Organization and Profiles definition**

In you organization, create a group 'Human Resources' and two roles: 'Novice member' & 'Experimented member'.  
Create some users with the membership 'novice member of human resources' and some others with the membership 'experimented member of human resources'.

Define two custom profiles using the [profile editor](profileCreation.md) in the Studio:  

 - **Novice HR**, mapped with the membership 'novice member of human resources' 
 - **Experimented HR**, mapped with the membership 'experimented member of human resources' 

**III - Create a process and generate some data**

Create a new diagram, with only a start event and an end event.  
On the pool, add a business variable of type *LeaveRequest*, generate a contract from this data, and generate an instantiation form from this contract.  
Run this process a couple of time to generate some LeaveRequest data.

**IV - Create a basic living application to display data**

 In the UI Designer,  create an application page (*displayLeaveRequest*):

 - Create a new variable
	 -  **Name** : leaveRequests
	 -  **Type** : External API
	 -  **API URL** :  ../API/bdm/businessData/com.company.model.LeaveRequest?q=find&p=0&c=10
 
 - Add a title to your page (Existing leave requests)
 - Add a container under the title
	 - **Collection**: leaveRequests 
	 - **CSS classes**: alert alert-info
 - Inside this container, for each of the following attributes of your Business object *(employee - departureDate - duration - medicalComment - managerComment)*:
	 - Add an input
		 - Label : [current attribute name]
		 - Value : $item.[current attribute name]
		 - Read-only: true

Create a new application descriptor using the [application editor](applicationCreation.md) in the Studio:  

 - Set the application token: leaveRequest
 - Set the Application Profile: User (ensure that the users mapped with the two profiles created in part II are also mapped with the profile User)
 - Add an orphan page:
	 - **Application Page**: custompage_displayLeaveRequest
	 - **Token**:  displayLeaveRequest
 - Set the Home page token: displayLeaveRequest
 - deploy

Ensure that the living application works correctly, and that all the attributes are displayed at the moment.

**V - Define access control for your business object LeaveRequest**

We want to ensure that only experimented member of HR can access to the attribute *medicalComment* of a leave request. A novice member of HR shall not see this attribute.  
For that, we are going to define two rules for our business object *LeaveRequest*:  

The first rule is the rule for the novices members of HR:

 - **Rule name**: Novice HR Access
 - **Rule description**: A novice member of HR can't see the medical comment of a leave request
 - **Attributes checked**:  [employee, departureDate, duration, managerComment]
 - **Profiles checked**: [Novice HR]

The second rule is the rule for the experimented members of HR:

 - **Rule name**: Experimented HR Access
 - **Rule description**: An experimented member of HR can see all the attributes of a leave request
 - **Attributes checked**:  [employee, departureDate, duration, medicalComment, managerComment]
 - **Profiles checked**: [Experimented HR]

Deploy your access control file

**VI - Access control validation**

The access to your previous data is now controlled by the BDM Access Control file you just deployed. A novice member of HR can't see the attribute *medicalComment* on a LeaveRequest anymore.  
Connect onto the portal as a user with the profile Novice HR. Go to your application , and ensure that the medical comment is always empty.  
Connect now as a user with the profile Experimented HR. On your application, you should see the medical comments.