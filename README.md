# Extension2.0

## Pre-requisites

### Set following environment variables  

```
export DTCLI_API_TOKEN=dt0c01.xxx.xxx

export DT_TENANT=https://abc.live.dynatrace.com/
```

### Set the directory structure  
```
myExtensionProject
│ 
└─── src
│   └───   extension.yaml
│   └───   alerts.json (optional - only if referenced in your custom extension.yaml)
│   └───   dashboard.json (optional and required if referenced in your custom extension.yaml)
└─── secrets
```

### Download the library you will need to build the extension
```
pip3.9 install dt-cli
```

## Assemble and push the modified extension or datasource

1. First step would be to assemble the extension. To do so, run the below command from `myExtensionProject` directory:

```
dt extension assemble
```
This will generate an extension.zip in `src` directory. To forcefully overwrite an existing extension.zip, run ```dt extension assemble --force``` instead.  
 

2. Generating self-signed certificate
   Now, generate self-signed certificate by running the below command from `myExtensionProject` directory:
   ```
   mkdir secrets
   cd secrets
   dt extension genca --no-ca-passphrase
   dt extension generate-developer-pem -o developer.pem --ca-crt ca.pem --ca-key ca.key --name 'Developer Name'
   ```
   
   This will generate certificates in secrets folder. Once generated, upload the secret on both AG and tenant.
   ⚠️ Please note that this workflow should only be used for demonstration / PoC purposes. We recommend following the security best practices such as having the root and developer certificates managed by different  
     entities as opposed to storing them in the same directory.
   
   2.1 Uploading certificate on tenant:
       The certificate will be generated in secrets folder. Upload it to tenant by following steps as below 🪜
       ```
       2.1.1 From the navigation menu, select Manage > Credential vault.
       2.1.2 Select Add new credential.
       2.1.3 For Credential type, select Public Certificate.
       2.1.4 Add a meaningful Credential name.
       2.1.5 Upload the Root certificate file (ca.pem).
       2.1.6 Select Save.
       ```
   
   2.2 Uploading certificate on ActiveGate(s):
       Similarly, upload the generated certificate (ca.pem) on all the AGs that you plan to run the extension from. Upload the certificate to location `/var/lib/dynatrace/remotepluginmodule/agent/conf/certificates/` within AG.

4. Pushing the modified extension:
   Lastly, push the extension by running the below command:
   ```
     dt extension assemble
     dt extension sign --key secrets/developer.pem
     dt extension validate --tenant-url $DT_TENANT --api-token $DTCLI_API_TOKEN bundle.zip
     dt extension upload --tenant-url $DT_TENANT --api-token $DTCLI_API_TOKEN bundle.zip
   ```
   


