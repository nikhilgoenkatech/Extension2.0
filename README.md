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
   Now, generate self-signed certificate by running the below command:
   ```
   dt extension generate-developer-pem -o developer.pem --ca-crt ca.pem --ca-key ca.key --name 'Nikhil'
   ```
   
   This will generate certificates in secrets folder. Once generated, upload the secret on both AG and tenant.
   
   2.1 Uploading certificate on tenant:
       The certificate will be generated in secrets folder. Upload it to tenant at
   
   2.2 Uploading certificate on ActiveGate(s):
       Similarly, upload the generated certificate on all the AGs that you plan to run the extension from. Upload the certificate to location `` within AG.

4. Pushing the modified extension:
   Lastly, push the extension by running the below command:
   ```
     dt extension upload --tenant-url $DT_TENANT --api-token $DTCLI_API_TOKEN bundle.zip
   ```
   


