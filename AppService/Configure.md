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