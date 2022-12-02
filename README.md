# tools.core
plugin library for tools

# Create your own plugin
First, download tools.core.dll or nupkg from release and add to .net project (.net6.0, output library).
Add Spectre.Console.nupkg and Spectre.Console.Cli.nupkg if needed

### Create HelloWorldCommand
```CSharp
using System.Diagnostics.CodeAnalysis;
using tools.core;

public class HelloWorldCommand : Command<HelloWorldCommand.Settings>
{
    public class Settings : CommandSettings
    {
        [CommandArgument(0, "[name]")]
        [Description("Name to greet")]
        public string Name { get; set; } = "";
    }

    public override int Execute([NotNull] CommandContext context, [NotNull] Settings settings)
    {
        AnsiConsole.MarkupLine($"Hello, [italic blue]{settings.Name}[/]");

        return 0;
    }
}
```
Notice the code above also create Settings class inherit CommandSettings, this required if any argument or option needed for the command
Read more: https://spectreconsole.net/cli/commands

Next create plugin class
### Create HelloWorldPlugin
```CSharp
using tools.core;

public class HelloWorldPlugin : Plugin
{
    public HelloWorldPlugin()
    {
        RootCommandKeys.Add(nameof(HelloWorldCommand), "greet");
    }

    public override string Name => "Hello World";

    public override string Author => "author";

    public override string Description => "Echo hello world";

    public override Version Version => new(1, 0);

    public override void Configure(IConfigurator config)
    {
        config.AddCommand<HelloWorldCommand>(RootCommandKeys[nameof(HelloWorldCommand)])
            .WithDescription("greet new user to programming world.");
    }
}
```
Then publish. 
```
dotnet publish
```

Personally i prefer to publish with the following profile
```Xml
<Project>
  <PropertyGroup>
    <Configuration>Release</Configuration>
    <Platform>Any CPU</Platform>
    <PublishDir>bin\Release\net6.0\publish\win-x64\</PublishDir>
    <PublishProtocol>FileSystem</PublishProtocol>
    <_TargetId>Folder</_TargetId>
    <TargetFramework>net6.0</TargetFramework>
    <RuntimeIdentifier>win-x64</RuntimeIdentifier>
    <SelfContained>true</SelfContained>
    <PublishSingleFile>true</PublishSingleFile>
    <PublishReadyToRun>true</PublishReadyToRun>
    <PublishTrimmed>true</PublishTrimmed>
  </PropertyGroup>
</Project>
```

Copy the files from publish directory to tools_plugin
### publish directory
![image](https://user-images.githubusercontent.com/62987279/205306878-05edc454-9943-4e14-a4e9-90dff3813428.png)

NOTE: Copy all other .dll that required for your plugin except tools.core to same directory as tools.exe

### tools_plugins directory
![image](https://user-images.githubusercontent.com/62987279/205306462-031c100e-85b0-4074-a32c-46a3d319abc9.png)

## Run tools
![image](https://user-images.githubusercontent.com/62987279/205308359-db9adb91-4b49-4d04-84eb-bd1048b592e8.png)

## Run plugin scan command
![image](https://user-images.githubusercontent.com/62987279/205309709-37ab4be9-6894-4fbb-b900-425c9e6514f2.png)

### Run greet command
![image](https://user-images.githubusercontent.com/62987279/205308185-36e34852-e06d-473f-be44-9f47a9652175.png)

Done. Your plugin is ready to run.

Thanks to [Spectre.Console](https://spectreconsole.net/) for amazing and colorful CommandApp, and definitely thanks to Ms .net6.0 :)
