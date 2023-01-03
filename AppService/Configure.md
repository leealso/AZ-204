# Configure web app settings
In App Service, app settings are variables passed as environment variables to the application code. For Linux apps and custom containers, App Service passes app settings to the container using the `--env` flag to set the environment variable in the container.

For ASP.NET and ASP.NET Core developers, setting app settings in App Service are like setting them in `<appSettings>` in _Web.config_ or _appsettings.json_, but the values in App Service override the ones in _Web.config_ or _appsettings.json_. 
App settings are always encrypted when stored (encrypted-at-rest).

In a default, or custom, Linux container any nested JSON key structure in the app setting name like `ApplicationInsights:InstrumentationKey` needs to be configured in App Service as `ApplicationInsights__InstrumentationKey` for the key name. In other words, any `:` should be replaced by `__` (double underscore).

## Configure connection strings
For ASP.NET and ASP.NET Core developers, the values you set in App Service override the ones in _Web.config_. For other language stacks, it's better to use app settings instead, because connection strings require special formatting in the variable keys in order to access the values. Connection strings are always encrypted when stored (encrypted-at-rest).

## Configure general settings
Some settings require you to scale up to higher pricing tiers. Below is a list of the currently available settings:
- *Stack settings*: The software stack to run the app, including the language and SDK versions. For Linux apps and custom container apps, you can also set an optional start-up command or file.
- *Platform settings*: Lets you configure settings for the hosting platform, including:
    - *Bitness*: 32-bit or 64-bit.
    - *WebSocket protocol*: For ASP.NET SignalR or socket.io, for example.
    - *Always On*: Keep the app loaded even when there's no traffic. By default, Always On is not enabled and the app is unloaded after 20 minutes without any incoming requests. It's required for continuous WebJobs or for WebJobs that are triggered using a CRON expression.
    - *Managed pipeline version*: The IIS pipeline mode. Set it to Classic if you have a legacy app that requires an older version of IIS.
    - *HTTP version*: Set to 2.0 to enable support for HTTPS/2 protocol.
    - *ARR affinity*: In a multi-instance deployment, ensure that the client is routed to the same instance for the life of the session. You can set this option to Off for stateless applications.
- Debugging: Enable remote debugging for ASP.NET, ASP.NET Core, or Node.js apps. This option turns off automatically after 48 hours.
- Incoming client certificates: require client certificates in mutual authentication. TLS mutual authentication is used to restrict access to your app by enabling different types of authentication for it.