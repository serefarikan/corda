Network permissioning
=====================

The keystore located in ``<workspace>/certificates/sslkeystore.jks`` is required to connect to the Corda network securely. 
In development mode (when ``devMode = true``, see :doc:`corda-configuration-file` for more information) a pre-configured keystore will be used if the keystore does not exist.
This is to ensure developers can get the nodes working as quickly as possible.

However this is not secure for the real network. This documentation will explain the procedure of obtaining a signed certificate for TestNet. 

.. warning:: The TestNet has not been setup yet as of Milestone 6 release. You will not be able to connect to the certificate signing server. 

Certificate signing request utility
-----------------------------------

The utility creates certificate signing request based on node information obtained from the node configuration. 
The following information from the node configuration file is needed to generate a certificate signing request.

:myLegalName: Your company's legal name. e.g. "R3 CEV, LLC"

:nearestCity:  e.g. "London"

:emailAddress: e.g. "admin@company.com"

:certificateSigningService: Certificate signing server URL. A certificate signing server will be hosted by R3 in the near future.  e.g."https://testnet.certificate.corda.net"

A new pair of private and public keys will be generated by the utility and will be used to create the request.

The utility will submit the request to the network permissioning server and poll for a result periodically to retrieve the certificates.
Once the request has been approved and the certificates downloaded from the server, the utility will create the key store and trust store using the certificates and the generated private key.

.. note:: You can exit the utility at anytime if the approval process is taking longer then expected. The request process will resume on restart. 

This process only needs to be done once when the node connects to the network for the first time, or when the certificate expires.

Building the utility
--------------------
The utility will be created as part of the gradle ``:node`` module ``buildCordaJAR`` task.
You can also build the utility JAR by run the following command from the Corda project root directory.

**Windows**::

    gradlew.bat :node:buildCertSigningRequestUtilityJAR

**Other**::

    ./gradlew :node:buildCertSigningRequestUtilityJAR

The utility JAR will be created in ``<Project Root Dir>/node/build/libs/certSigningRequestUtility.jar``


Running the utility
-------------------

You will need to specify the working directory of your Corda node using ``--base-dir`` flag. This is defaulted to current directory if left blank.
You can also specify the location of ``node.conf`` with ``--config-file`` flag if it's not in the working directory.

**Running the Utility**::

    java -jar certSigningRequestUtility.jar --base-dir <<optional>> --config-file <<optional>>

A ``certificates`` folder containing the keystore and trust store will be created in the base directory when the process is completed.

.. warning:: The keystore is protected by the keystore password from the node configuration file. The password should kept safe to protect the private key and certificate.
.. note:: Password encryption in node configuration will be supported in subsequent release. 