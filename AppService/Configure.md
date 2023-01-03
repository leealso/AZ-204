# Configure web app settings
In App Service, app settings are variables passed as environment variables to the application code. For Linux apps and custom containers, App Service passes app settings to the container using the `--env` flag to set the environment variable in the container.

For ASP.NET and ASP.NET Core developers, setting app settings in App Service are like setting them in `<appSettings>` in _Web.config_ or _appsettings.json_, but the values in App Service override the ones in _Web.config_ or _appsettings.json_. 
App settings are always encrypted when stored (encrypted-at-rest).

In a default, or custom, Linux container any nested JSON key structure in the app setting name like `ApplicationInsights:InstrumentationKey` needs to be configured in App Service as `ApplicationInsights__InstrumentationKey` for the key name. In other words, any `:` should be replaced by `__` (double underscore).

## Configure connection strings
For ASP.NET and ASP.NET Core developers, the values you set in App Service override the ones in _Web.config_. For other language stacks, it's better to use app settings instead, because connection strings require special formatting in the variable keys in order to access the values. Connection strings are always encrypted when stored (encrypted-at-rest).