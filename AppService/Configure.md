# Configure web app settings
In App Service, app settings are variables passed as environment variables to the application code. For Linux apps and custom containers, App Service passes app settings to the container using the `--env` flag to set the environment variable in the container.

For ASP.NET and ASP.NET Core developers, setting app settings in App Service are like setting them in `<appSettings>` in _Web.config_ or _appsettings.json_, but the values in App Service override the ones in _Web.config_ or _appsettings.json_. 
App settings are always encrypted when stored (encrypted-at-rest).

In a default, or custom, Linux container any nested JSON key structure in the app setting name like `ApplicationInsights:InstrumentationKey` needs to be configured in App Service as `ApplicationInsights__InstrumentationKey` for the key name. In other words, any `:` should be replaced by `__` (double underscore).

## Configure connection strings
For ASP.NET and ASP.NET Core developers, the values you set in App Service override the ones in _Web.config_. For other language stacks, it's better to use app settings instead, because connection strings require special formatting in the variable keys in order to access the values. Connection strings are always encrypted when stored (encrypted-at-rest).

## Configure general settings
Some settings require you to scale up to higher pricing tiers. Below is a list of the currently available settings:
- **Stack settings**: The software stack to run the app, including the language and SDK versions. For Linux apps and custom container apps, you can also set an optional start-up command or file.
- **Platform settings**: Lets you configure settings for the hosting platform, including:
    - **Bitness**: 32-bit or 64-bit.
    - **WebSocket protocol**: For ASP.NET SignalR or socket.io, for example.
    - **Always On**: Keep the app loaded even when there's no traffic. By default, Always On is not enabled and the app is unloaded after 20 minutes without any incoming requests. It's required for continuous WebJobs or for WebJobs that are triggered using a CRON expression.
    - **Managed pipeline version**: The IIS pipeline mode. Set it to Classic if you have a legacy app that requires an older version of IIS.
    - **HTTP version**: Set to 2.0 to enable support for HTTPS/2 protocol.
    - **ARR affinity**: In a multi-instance deployment, ensure that the client is routed to the same instance for the life of the session. You can set this option to Off for stateless applications.
- **Debugging**: Enable remote debugging for ASP.NET, ASP.NET Core, or Node.js apps. This option turns off automatically after 48 hours.
- **Incoming client certificates**: require client certificates in mutual authentication. TLS mutual authentication is used to restrict access to your app by enabling different types of authentication for it.

## Configure path mappings
You can configure handler mappings, and virtual application and directory mappings. The Path mappings page will display different options based on the OS type.

### Windows apps (uncontainerized)
For Windows apps, you can customize the IIS handler mappings and virtual applications and directories.
Handler mappings let you add custom script processors to handle requests for specific file extensions.
- **Extension**: The file extension you want to handle, such as _*.php_ or _handler.fcgi_.
- **Script processor**: The absolute path of the script processor. Requests to files that match the file extension are processed by the script processor. Use the path `D:\home\site\wwwroot` to refer to your app's root directory.
- **Arguments**: Optional command-line arguments for the script processor.
Each app has the default root path (`/`) mapped to `D:\home\site\wwwroot`, where your code is deployed by default. If your app root is in a different folder, or if your repository has more than one application, you can edit or add virtual applications and directories.
You can configure virtual applications and directories by specifying each virtual directory and its corresponding physical path relative to the website root (`D:\home`). To mark a virtual directory as a web application, clear the Directory check box.

### Linux and containerized apps
You can add custom storage for your containerized app. Containerized apps include all Linux apps and also the Windows and Linux custom containers running on App Service.
- **Name**: The display name.
- **Configuration options**: Basic or Advanced.
- **Storage accounts**: The storage account with the container you want.
- **Storage type**: Azure Blobs or Azure Files. Windows container apps only support Azure Files.
- **Storage container**: For basic configuration, the container you want.
- **Share name**: For advanced configuration, the file share name.
- **Access key**: For advanced configuration, the access key.
- **Mount path**: The absolute path in your container to mount the custom storage.

## Enable diagnostic logging
Type | Platform | Location | Description
--- | --- | --- | ---
Application logging	 | Windows, Linux | App Service file system and/or Azure Storage blobs | Logs messages generated by your application code. The messages can be generated by the web framework you choose, or from your application code directly using the standard logging pattern of your language. The Filesystem option is for temporary debugging purposes, and turns itself off in 12 hours. The Blob option is for long-term logging, and needs a blob storage container to write logs to.
Web server logging | Windows | App Service file system or Azure Storage blobs | Raw HTTP request data in the W3C extended log file format. Each log message includes data like the HTTP method, resource URI, client IP, client port, user agent, response code, and so on.
Detailed error logging | Windows | App Service file system | Copies of the .html error pages that would have been sent to the client browser.
Failed request tracing | Windows | App Service file system | Detailed tracing information on failed requests, including a trace of the IIS components used to process the request and the time taken in each component.
Deployment logging | Windows, Linux | App Service file system | Helps determine why a deployment failed. Deployment logging happens automatically and there are no configurable settings for deployment logging.

### Add log messages in code
- ASP.NET applications can use the `System.Diagnostics.Trace` class to log information to the application diagnostics log.
- By default, ASP.NET Core uses the `Microsoft.Extensions.Logging.AzureAppServices` logging provider.

### Access log files
If you configure the Azure Storage blobs option for a log type, you need a client tool that works with Azure Storage.
For logs stored in the App Service file system, the easiest way is to download the ZIP file in the browser at:
- **Linux/container apps**: `https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip`
- **Windows apps**: `https://<app-name>.scm.azurewebsites.net/api/dump`

## Configure security certificates
App Service has tools that let you create, upload, or import a private certificate or a public certificate into App Service.

A certificate uploaded into an app is stored in a deployment unit that is bound to the app service plan's resource group and region combination (internally called a webspace). This makes the certificate accessible to other apps in the same resource group and region combination.

Options you have for adding certificates in App Service:
Option	| Description
--- | ---
Create a free App Service managed certificate | A private certificate that's free of charge and easy to use if you just need to secure your custom domain in App Service.
Purchase an App Service certificate | A private certificate that's managed by Azure. It combines the simplicity of automated certificate management and the flexibility of renewal and export options.
Import a certificate from Key Vault	| Useful if you use Azure Key Vault to manage your certificates.
Upload a private certificate | If you already have a private certificate from a third-party provider, you can upload it.
Upload a public certificate	| Public certificates are not used to secure custom domains, but you can load them into your code if you need them to access remote resources.

### Private certificate requirements
If you want to use a private certificate in App Service, your certificate must meet the following requirements:
- Exported as a password-protected PFX file, encrypted using triple DES.
- Contains private key at least 2048 bits long
- Contains all intermediate certificates in the certificate chain

To secure a custom domain in a TLS binding, the certificate has additional requirements:
- Contains an Extended Key Usage for server authentication (OID = 1.3.6.1.5.5.7.3.1)
- Signed by a trusted certificate authority

### Free App Service managed certificate limitations
- Does not support wildcard certificates.
- Does not support usage as a client certificate by certificate thumbprint.
- Is not exportable.
- Is not supported on App Service Environment (ASE).
- Is not supported with root domains that are integrated with Traffic Manager.
- If a certificate is for a CNAME-mapped domain, the CNAME must be mapped directly to `<app-name>.azurewebsites.net`.

### App Service certificate
- If you purchase an App Service Certificate from Azure, Azure manages the following tasks:
- Takes care of the purchase process from GoDaddy.
- Performs domain verification of the certificate.
- Maintains the certificate in Azure Key Vault.
- Manages certificate renewal.
- Synchronize the certificate automatically with the imported copies in App Service apps.

### Upload a private certificate
If your certificate authority gives you multiple certificates in the certificate chain, you need to merge the certificates in order. Then you can Export your merged TLS/SSL certificate with the private key that your certificate request was generated with.

If you generated your certificate request using OpenSSL, then you have created a private key file. To export your certificate to PFX, run the following command. 
`openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>`

## Manage app features
Feature management is a modern software-development practice that decouples feature release from code deployment and enables quick changes to feature availability on demand. It uses a technique called feature flags (also known as feature toggles, feature switches, and so on) to dynamically administer a feature's lifecycle.

Here are several new terms related to feature management:
- **Feature flag**: A feature flag is a variable with a binary state of _on_ or _off_. The feature flag also has an associated code block. The state of the feature flag triggers whether the code block runs or not.
- **Feature manager**: A feature manager is an application package that handles the lifecycle of all the feature flags in an application. The feature manager typically provides additional functionality, such as caching feature flags and updating their states.
- **Filter**: A filter is a rule for evaluating the state of a feature flag. A user group, a device or browser type, a geographic location, and a time window are all examples of what a filter can represent.

An effective implementation of feature management consists of at least two components working in concert:
- An application that makes use of feature flags.
- A separate repository that stores the feature flags and their current states.

### Feature flag declaration
Each feature flag has two parts: a name and a list of one or more filters that are used to evaluate if a feature's state is _on_ (that is, when its value is `True`). A filter defines a use case for when a feature should be turned on.

When a feature flag has multiple filters, the filter list is traversed in order until one of the filters determines the feature 
should be enabled. At that point, the feature flag is _on_, and any remaining filter results are skipped. If no filter indicates the feature should be enabled, the feature flag is _off_.

The feature manager supports _appsettings.json_ as a configuration source for feature flags. The following example shows how to set up feature flags in a JSON file:
```
"FeatureManagement": {
    "FeatureA": true, // Feature flag set to on
    "FeatureB": false, // Feature flag set to off
    "FeatureC": {
        "EnabledFor": [
            {
                "Name": "Percentage",
                "Parameters": {
                    "Value": 50
                }
            }
        ]
    }
}
```

### Feature flag repository
To use feature flags effectively, you need to externalize all the feature flags used in an application. This approach allows you to change feature flag states without modifying and redeploying the application itself.

Azure App Configuration is designed to be a centralized repository for feature flags. You can use it to define different kinds of feature flags and manipulate their states quickly and confidently. You can then use the App Configuration libraries for various programming language frameworks to easily access these feature flags from your application.