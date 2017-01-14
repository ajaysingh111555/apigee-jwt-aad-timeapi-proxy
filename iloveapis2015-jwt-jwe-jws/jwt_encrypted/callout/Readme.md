# jwt_encrypted callout

This directory contains the Java source code and Java jars required to
compile a Java callout for Apigee Edge that does generation and
parsing / validation of encrypted (JWE) JWT. It uses the Jose4J library.

Building:
--------


# JWT Encrypted callout

This directory contains the Java source code and pom.xml file required
to compile a Java callout for Apigee Edge that creates and validates an Encrypted JWT.
. It uses the Jose4J library for JOSE (JSON Object Signing and
Encryption) support.

Building:
--------

You can use the Java callout binary, or you can build the binary yourself.
These instructions describe how to do either.

1. unpack (if you can read this, you've already done that).

2. build the binary with maven:
   ```
   mvn clean package
   ```

3. maven will copy all the required jar files to your apiproxy/resources/java directory.
   If for some reason your project directory is not set up properly, you can do this manually.
   copy target/jwt-encrypted-callout.jar to your apiproxy/resources/java directory.
   Also copy from the target/lib directory, these jars:
     jose4j-0.4.4.jar

4. be sure to include a Java callout policy in your
   apiproxy/resources/policies directory. It should look like
   this:
    ```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<JavaCallout async="false" continueOnError="false" enabled="true" name="JWT_Encrypted_Creator_Callout">
    <DisplayName>JWT_Encrypted_Creator_Callout</DisplayName>
    <Properties>
  <Property name="stepname">JavaCallout-JWE-Create</Property>
  <Property name="expirationInMinutes">60</Property>
  <Property name="issuer">{client_id}</Property>
   ```

5. Deploy your API Proxy, using
   Maven (explained in the readme for API proxy)


Dependencies
------------------

Jars available in Edge:
 - Apigee Edge expressions v1.0
 - Apigee Edge message-flow v1.0
 - Apache commons lang v2.6 - String and Date utilities
 - Apache commons codec 1.7 - Base64 decoder
 - not-yet-commons-ssl v0.3.9 - RSA private/public crypto

Jars not available in Edge:
 - Jose4J v0.4.4

All these jars must be available on the classpath for the compile to
succeed. The build.sh script should download all of these files for
you, automatically.



Configuring the Callout Policy:
----------------------------

There are two callout classes, one to create an encrypted JWT and one to decrypt and validate an encrypted JWT.

How the encrypted JWT is generated or decrypted, respectively,
depends on configuration information you specify for the callout, in the
form of properties on the policy.  Some examples follow.



**Creating an encrypted JWT**

<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<JavaCallout async="false" continueOnError="false" enabled="true" name="JWT_Encrypted_Creator_Callout">
    <DisplayName>JWT_Encrypted_Creator_Callout</DisplayName>
    <Properties>
  <Property name="stepname">JavaCallout-JWE-Create</Property>
  <Property name="expirationInMinutes">60</Property>
  <Property name="issuer">{client_id}</Property>
```



For information on the meaning of these algorithms, see section 5 of the JWS spec:
https://tools.ietf.org/html/rfc7518.


**Validating an encrypted JWT**

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<JavaCallout async="false" continueOnError="false" enabled="true" name="JWT_Encrypted_Validator_Callout">
    <DisplayName>JWT_Encrypted_Validator_Callout</DisplayName>
    <Properties>
        <Property name="stepname">JavaCallout-JWE-Validate</Property>
        <Property name="jwt">{request.formparam.jwt}</Property>
        <!-- private-key and private-key-password used only for algorithm = RS256 -->
        <Property name="private-key-password">*****</Property>
    </Properties>
    <ClassName>com.apigee.callout.jwt_encrypted.JWT_Encrypted_Validator_Callout</ClassName>
    <ResourceURL>java://jwt-encrypted-edge-callout.jar</ResourceURL>
</JavaCallout>
```


More Notes:
--------







....
