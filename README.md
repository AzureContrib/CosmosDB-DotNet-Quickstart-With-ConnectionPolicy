# Welcome to CosmosDb.QuickStart sample using Connection Policy to Optimize Client Side Requests

Hi! I out together a sample quick start to help you all in optimizing your Cosmos Db .NET SDK integration logic using **Microsoft.Azure.Documents.Client.ConnectionPolicy**. 

# Prerequisites 
1. Windows 7/8.x/10 with Visual Studio 2015/2017 with .NET Framework 4.5
2. Nuget Package Manager 
3. Setup a CosmosDb environment in Azure or use Cosmos Db Emulator (on Windows 10).
# How to use the application?

1. Open **CosmosDb-QuickStart.sln**  
2. Do a Nuget package **restore**.
3. **Update** the Web.config with your cosmosDb URL and authKey. 
4. Launch the solution in **debug** mode. 

# Important Files to Consider 

**Web.config** - All the configuration for the connection policy are driven from Web.config-> appSettings, so that application can pick up right state during Run Time based on the configurations defined. There is some hard coded values, which are not made it as configurable. 

**Constants.cs** -  I just wanted to simply the way we call ConfigurationManager.AppSettings, by defining them only once as a static **readonly** variable. 

      //Constants.cs -> Snippet
      public static readonly string database = ConfigurationManager.AppSettings["database"];
      public static readonly string collection = ConfigurationManager.AppSettings["collection"];
 
      public static readonly string endpoint = ConfigurationManager.AppSettings["endpoint"];
      public static readonly string authKey = ConfigurationManager.AppSettings["authKey"];
 
      public static readonly string connectionMode = ConfigurationManager.AppSettings["connectionMode"];
      public static readonly string connectionProtocol = ConfigurationManager.AppSettings["connectionProtocol"];
 
      public static readonly int maxConnectionLimit = Convert.ToInt32(ConfigurationManager.AppSettings["maxConnectionLimit"]);

**CosmosDbRepository.cs** - Refer to the Initialize method. 


       //Default: Direct Mode, based on appSettings configuration, application would pick up right connection mode in Run Time.
       var connectionMode = Constants.connectionMode.Trim().Equals("direct", StringComparison.OrdinalIgnoreCase) ? ConnectionMode.Direct : ConnectionMode.Gateway;                                                                                                                                                 
       
       //Default: HTTPS, based on appSettings configuration, application would pick up right connection mode in Run Time.
       var connectionProtocol = Constants.connectionProtocol.Trim().Equals("tcp", StringComparison.OrdinalIgnoreCase) ? Protocol.Tcp : Protocol.Https;
       var maxConnectionLimit = Constants.maxConnectionLimit;
       //Setting up a Connection Policy to be passed on while creating DocumentClient
       ConnectionPolicy connectionPolicy = new ConnectionPolicy
       {
              ConnectionMode = connectionMode,
              ConnectionProtocol = connectionProtocol,
              MaxConnectionLimit = maxConnectionLimit,
              RetryOptions = new RetryOptions() { MaxRetryAttemptsOnThrottledRequests = 5, MaxRetryWaitTimeInSeconds = 5 } // setting a retry for Connection Throttling and Retry.
       };
       client = new DocumentClient(new Uri(Constants.endpoint), Constants.authKey, connectionPolicy);

> **ProTip:** Source code is provided as is based on  **CosmosDb Basic ToDo starter application**  and I customized it to showcase the **ConnectionPolicy** implementation.

> **More Reads:** If you want to learn about ConnectionPolicy class, you can read [here](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.documents.client.connectionpolicy?view=azure-dotnet). 