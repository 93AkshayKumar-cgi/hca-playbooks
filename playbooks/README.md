# Automation for NetApp ONTAP 9.x and Cluster Builds with Ansible 2.9x and 2.10

  
  

## About

  

This repo delivers a single playbook (site.yml) and accompanying components to diliver a single source

automation process for both creating NetApp Clusters (Day 0) as well as creating and managing resources in

* a NetApp ONTAP 9.x running cluster.


## Features

  

Using custom reusable roles, the consumer is able to create multiple "services" with varying standards and configurations aspects within the repo all the while, using the same single playbook and play.

Through a weighted process, components can support a variety of settings and configurations in the same play, thus allowing the consumer to create custom services that meet their specifi evironment needs and requirements. Through a weighted configuration model of playbook input data, playbook defaults and playbook standards a consumer has the ability to
ensure enterprise requirements are delivered consistently.

  

## Overview

  

This set of Ansible playbooks and associated roles provides a framework

to support ONTAP upgrade using a set of services defined by the customer.

  

The framework consists of several main components:

1. site.yml

	*site.yml* serves as the entry point for all defined services and typically will not be changed for a particular implementation.


2. Service definitions (services)

	*	A Service definition is a logical definition of actions/instructions that will execute when the playbook is invoked.
	*	A play **MUST** call a defined service to execute
	*	Refer to DEFINITIONS below.

4. Service Request

	*	The *service request* is a predefined service definition called for a particular invocation of the service and contains service specific attribute/value pairs for a single execution request.
		*	 e.g. `Provision an SVM`, `Initial Cluster Build`, etc.

	*	The *service request*, or payload, is built externally by the mechanism(s) making calls to run the site.yml playbook.
	*	The *service request* is passed as an extra variable to the playbook.

5. Service Defaults

	*	 *Service defaults* provide values for a particular service request when the service request does not include them.

	*	*Service defaults* provide a way to define a complete request while having to supply only a minimum of information in the request itself.

	*	*Service defaults* do not override values that exist in the request.

6. Service Standards

	*	*Service standards* are similar to service defaults with one significant difference: standards override values in the provided request even if a value is provided within the request for the associated attribute.

7. Named Standards

	* *Named standards* apply a name to a combination of defaults and standards defined above.

	*	 A service request includes the name of the standard that should be applied to the request, which implies defaults and standard values are also applied.

	*	This provides a means to define multiple standards (e.g. for different hardware platforms) and in turn dictate what attribute/values will be used for a particular service request.

  

## Installation

  

* download the repo from here: [CGI BITBUCKET](https://proactionus.ent.cgi.com/bitbucket/projects/NETAPP/repos/deutsche-bank/browse?at=refs%2Fheads%2Fdevelop)

## Usage
From the repo $INSTALL_DIR
> **_ansible-playbook site.yml_**

## Configuration

  
#### Directory Structure
>The repo will deliver three top level directories: 

> **NEED TO CONFIRM B4 RELEASE**

$INSTALL_DIR/
						 ~/bin
                         ~/playbook
                         ~/wfa
            
 #### Configuration Files
 >  Installed here:  
		- $INSTALL_DIR/playbook/inventory/group_vars/all
		
####	Service Definitions Configuration Files
- **cfg.yml** : Services definitions are located here
	 - Configuration file for defining services that will be called and executed by the playbook
	 - Services definitions also include the operation and microservices necessary to deliver the desired outcome of the service
	 - A service **MUST** be defined here for the play to run


- **defaults.yml** : Location for default settings of a defined services microservices
	- defaults settings override user input or payload settings
	- A place to ensure mandatory minimums are configured for a defined service to run

- **standards.yml** : Location for the standard settings of a service role being called

	- Settings in the standards file over ride both user input settings and defaults settings
	- Enforces defined standards for defined services
	- Establishes consistent delivery
	- Allows enterprises to create, automate and enforce multiple data storage policy standards and configurations across the enterprise

#### Additional configuration files
- **clusters.yml**	:	a list of Netapp Clusters you wish to run the playbook against
- **vault.yml**	:	encrypted NetApp Cluster usernames and passwords
- **log_vars.yml**	:	logging variables based on localized requirements.  Defaults to /tmp/$file_var on the ansible host
- **cyberark.yml**	:	Variables for CYBERARK password retrieval requirements


#### DEFINITIONS and CFG.YML - A bit more detail
 **Service**

> A **service** is an ordered list of of microservices (roles) that are executed in the order defined in the service definition stanza in **cfg.yml.** 
Each microservice entry can have 0 or more instances in a particular

**execution request (payload)**
 A full service definition consists of: 
 ~~~
	1. Service Name
	   2. Operation
	     3. Microservice(s)  
	        -name
	        -args
	        -role
**NOTE: Services can have 1 or more microservices**
  ~~~

Each microservice definition consists of:
~~~
1. Name of the microservice
2. The name of the argument as it will appear in the service request.
3. The role that implements the microservice.
~~~

The cfg.yml is a yaml file that follows the CRUD model for building services. 
It is the roadmap to services(workflows) the customer wants to define.

**Services must be defined in cfg.yml before they can be used in the play.**

Our standard for building services using the following key value structure:
>services:
>>name_of_service
>>>CRUD (Create, Read, Update, Delete)
>>>>microservices:
>>>>- list of services, args and roles required to build the service

Example of a simple ONTAP_VOLUME Services
>services:
>>add_volume
>>>create
>>>>microservices:
>>>>> -	name:	ontap_volume
>>>>>	-	args	:	ontap_volume
>>>>>	-	role	:	na/ontap_volume
