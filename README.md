# Introduction

This demo was created to review some general features of JBoss Fuse 6.0. <br/>
It was created to help new fuse developers to understand how to create web services using different approaches. This services will execute basic mathematical operations (sum, add, multiply). We will create an additional web service that will standardize request to the other services and act as a proxy too.<br/><br/>

ENJOY!!!!

# Web Services Detail

We will create four web services which are: <br/><br/>
 * Sum Web Service:  It will be created using **Code first** approach. SOAP request will receive two numbers and return a sum operation. 
The wsdl definition will be available at `http://localhost:9000/sum/?wsdl`.<br/>
This is a SOAP Request and SOAP Response example:<br/>
SOAP Request: <br/>
```XML
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:wss="http://wssuma.ws.demos.fuse.redhat.com/">
   <soapenv:Header/>
   <soapenv:Body>
      <wss:sum>
         <arg0>
            <oper1>5</oper1>
            <oper2>1</oper2>
         </arg0>
      </wss:sum>
   </soapenv:Body>
</soapenv:Envelope>
```
SOAP Response: <br/>
```XML
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:wss="http://wssuma.ws.demos.fuse.redhat.com/">
   <soapenv:Header/>
   <soapenv:Body>
      <wss:sumResponse>
         <return>
            <result>6</result>
         </return>
      </wss:sumResponse>
   </soapenv:Body>
</soapenv:Envelope>
```

 * Add Web Service:  It will be created using **Code first** approach. SOAP request will receive two numbers and return an add operation.  The wsdl definition will be available at `http://localhost:9000/add/?wsdl`.<br/>
This is a SOAP Request and SOAP Response example:<br/>
SOAP Request: <br/>
```XML
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:wss="http://wssuma.ws.demos.fuse.redhat.com/">
   <soapenv:Header/>
   <soapenv:Body>
      <wss:add>
         <arg0>
            <oper1>3</oper1>
            <oper2>2</oper2>
         </arg0>
      </wss:add>
   </soapenv:Body>
</soapenv:Envelope>
```
SOAP Response: <br/>
```XML
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:wss="http://wssuma.ws.demos.fuse.redhat.com/">
   <soapenv:Header/>
   <soapenv:Body>
      <wss:addResponse>
         <return>
            <result>1</result>
         </return>
      </wss:addResponse>
   </soapenv:Body>
</soapenv:Envelope>
```


 * Multiply Web Service:  It will be created using **Contract first** approach. SOAP request will receive two numbers and return a multiply operation.  
The wsdl definition will be available at `http://localhost:9000/multiply/?wsdl`.<br/>
This is a SOAP Request and SOAP Response example:<br/>
SOAP Request: <br/>
```XML
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:wss="http://wssuma.ws.demos.fuse.redhat.com/">
   <soapenv:Header/>
   <soapenv:Body>
      <wss:multiply>
         <multiplyDTO>
            <oper1>2</oper1>
            <oper2>5</oper2>
         </multiplyDTO>
      </wss:multiply>
   </soapenv:Body>
</soapenv:Envelope>
```
SOAP Response: <br/>
```XML
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:wss="http://wssuma.ws.demos.fuse.redhat.com/">
   <soapenv:Header/>
   <soapenv:Body>
      <wss:multiplyResponse>
         <return>
            <result>10</result>
         </return>
      </wss:multiplyResponse>
   </soapenv:Body>
</soapenv:Envelope>
```
* Calculate Web Service: It will be created using **Contract first** approach. SOAP request will receive two numbers, a sender name and an operation type. The operation type can be **sum**, **add** or **multiply**. The service will return the resulting operation and an operation id constant **completed**.  <br/>
The wsdl definition will be available at `http://localhost:9000/calculate/?wsdl`.<br/>
This is a SOAP Request and SOAP Response example:<br/>
SOAP Request: <br/>
```XML
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:wss="http://wssuma.ws.demos.fuse.redhat.com/">
   <soapenv:Header/>
   <soapenv:Body>
      <wss:calculate>
            <operType>sum</operType>
            <sender>isaac</sender>
            <cuerpo>
            	<oper1>10</oper1>
            	<oper2>6</oper2>
            </cuerpo>
      </wss:calculate>
   </soapenv:Body>
</soapenv:Envelope>
```
SOAP Response: <br/>
```XML
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:wss="http://wssuma.ws.demos.fuse.redhat.com/">
   <soapenv:Header/>
   <soapenv:Body>
      <wss:calculateResponse>
         <operationId>completed</operationId>
         <responseObject>16</responseObject>
      </wss:calculateResponse>
   </soapenv:Body>
</soapenv:Envelope>
```

Note how SOAP request and responses are different for sum, add and multiply services. If a client wants to consume them, it will need to decide which operation request to use and know how operation response will look like depending on the service call. To make things easy for the client, we will expose calculate web service in order to standardize request and response. The client will only change the operation type value an calculate web service will:<br/>
1. Process request and response as xml text. No use of java binding (The sum, add and multiply services use java binding so you can see the differences)
2. Get the standardize request.
3. Set a target namespace header
4. Decide based on **operType** tag, which web service to call.
5. Transform the original request into real web service request depending on **operType**. This transformation will execute using XSLT.
6. Validate the resulting transformation agains an XSD schema file.
6. Invoke the real operation service
7. Transform the operation service response into a standardize response using XSLT transformations
8. Return response to client.

Here is a visual camel route of calculate service:<br/>
![Camel Route](https://github.com/igl100/FuseWSDemo/blob/master/docs/image/Capture0.png)

## Objectives

This demo will include information about several topics wich include: 

* Create a JBoss Fuse installation and initial configuration.
* Understand how to use Karaf console
* Understand how to Begin a Fabric configuration 
* Access Web console to manage Fuse and Fabrics
* Learn how to deploy projects profiles using fabric8 maven plugin
* Learn how to create web services using coding first approach
* Learn how to create web services using contract first approach
* Learn how to expose a proxy web service
* Learn how to route to different web services
* Learn how to use XSLT transformations
* Learn how to validate XML’s agains XSD schema.

## Pre-Requisites

1. JBoss Fuse 6.0 zip installation file 
2. Java JDK 7 installed
3. Apache Maven 3.1.1 version installed
4. Web Browser
5. Basic Linux commands understanding
6. SOAP UI or another web service client
7. Internet connection

# Setup JBoss Fuse

## Install JBoss Fuse

1. Open a command terminal

1. Unzip JBoss Fuse on any directory that you wish to use as $FUSE_HOME. In this example i will use directory `/opt/redhat/`. Copy JBoss Fuse installation zip file on the selected directory and be sure your user have read, write and execute privileges.

	- `cd /opt/redhat`
	- `unzip jboss-fuse-full-6.1.0.redhat-379.zip`<br/>
    ![Unzip Command](https://github.com/igl100/JBossFuseHADemo/blob/master/docs/image/Capture1.png)
    
	- `export FUSE_HOME=/opt/redhat/jboss-fuse-full-6.1.0.redhat-379`

	Thats it!!!, JBoss Fuse is already install!!!
 
## Configure JBoss Fuse 
 
Before running JBoss Fuse for the first time we need to configure user/password access.

1. Enable user/password for karaf console. On your opened terminal execute:
	- `cd $FUSE_HOME`
	- `vi ./etc/users.properties` (If you do not like vi, use any other text editor)
	- Uncomment the final line by removing # character from #admin=admin,admin line
	- Save the file (esc, :wq)

## Running JBoss Fuse

1. On opened terminal `$FUSE_HOME/bin/start`

2. Access karaf console:
	-  `./bin/client -u admin -p admin` 
    <br/>If you get a message **"Failed to get the session"** wait a few seconds and try again. This message means that JBoss Fuse is starting.<br/>
	![Karaf Console](https://github.com/igl100/JBossFuseHADemo/blob/master/docs/image/Capture2.png)

3. Create a fabric so we can manage all the brokers from a single console:
	- `fabric:create --clean --wait-for-provisioning  --bind-address localhost --resolver manualip --global-resolver manualip --manual-ip localhost --zookeeper-password admin`<br/><br/>
    All this parameters are needed so that zookeeper and fuse fabric bind everything to **localhost** address. This is not what you need to do on production servers but since ipaddress might change on laptops or PC's fabric might not start correctly on different networks.
    
4. Validate that fabric created by running `container-list` on karaf console.
	<br/>
	![Container-list command](https://github.com/igl100/JBossFuseHADemo/blob/master/docs/image/Capture3.png)

5. Open URL http://localhost:8181 on a web browser and login with user admin and password admin<br/>
	![Fabric Login](https://github.com/igl100/JBossFuseHADemo/blob/master/docs/image/Capture4.png)
    <br/>
    ![Fabric Home](https://github.com/igl100/JBossFuseHADemo/blob/master/docs/image/Capture5.png)
    <br/>
    ![Fabric Containers](https://github.com/igl100/JBossFuseHADemo/blob/master/docs/image/Capture6.png)
