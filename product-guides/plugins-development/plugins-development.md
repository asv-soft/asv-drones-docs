# Plugin Development Guide

## Introduction

This guide will walk you through the process of developing plugins for the `Asv.Drones.Gui` project. We'll use `Asv.Drones.Gui.Plugin.Weather` as an example as it's a simple project mainly geared towards educational purposes.

## Project Naming

Once you've decided on a project name, follow the plugin naming rule. The main application (`Asv.Drones.Gui`) uses a composition container to load external libraries, which implies that your plugins should be implemented as libraries. Moreover, your library files should follow the naming format `Asv.Drones.Gui.Plugin.**YourPluginName**`. In our case, it will be `Asv.Drones.Gui.Plugin.Weather`. This naming convention is crucial for the composition container to recognize and incorporate your plugin during the program start.

![](images/project-creation.png)

## Project Structure and Dependencies

The structure of your files and folders should mirror that of Asv.Drones.Gui. The final project structure is depicted below.

![](images/final-project-structure.png)

You can see that `Asv.Drones.Gui` project is present in our plugin solution. You need to add it as a Git submodule into your solution root folder as displayed in the image below.

![](images/plugin-root-folder-structure.png)

Next, manually add all the existing projects from `Asv.Drones.Gui`.

Ensure the addition of crucial dependencies such as:

* Resource files (RS.resx) - necessary for text localization.
* App.axaml file - helps in importing and exporting styles and custom controls.
* Asv.Drones.Gui.Custom.props file - used for managing the versions of required NuGet packages.

Below is the structure of `Asv.Drones.Gui.Custom.props` file. Copy this structure as it mentions the versions of all critical NuGet packages.
```xml
<Project>
  <ItemGroup>
    <ProjectReference Condition="'$(ProjectName)' == 'Asv.Drones.Gui'" Include="$(SolutionDir)$(SolutionName)\$(SolutionName).csproj" >
    </ProjectReference>
  </ItemGroup>
  <PropertyGroup>
    <Nullable>enable</Nullable>
    <ProductVersion>0.1.0</ProductVersion>
    <AvaloniaVersion>11.0.5</AvaloniaVersion>
    <AsvCommonVersion>1.13.1</AsvCommonVersion>
    <AsvMavlinkVersion>3.6.0-alpha11</AsvMavlinkVersion>
    <FluentAvaloniaUIVersion>2.0.0</FluentAvaloniaUIVersion>
    <ReactiveUIVersion>19.3.3</ReactiveUIVersion>
    <MaterialIconsAvaloniaVersion>2.0.1</MaterialIconsAvaloniaVersion>
    <ReactiveUIValidationVersion>3.1.7</ReactiveUIValidationVersion>
    <CompositionVersion>7.0.0</CompositionVersion>
    <NLogVersion>5.2.6</NLogVersion>
  </PropertyGroup>
</Project>
```

And you must do the same thing into Weather project file.

```xml
<Project Sdk="Microsoft.NET.Sdk">
  <Import Project="$(SolutionDir)Asv.Drones.Gui.Custom.props"/>

  <PropertyGroup>
    <TargetFramework>net7.0</TargetFramework>
    <ImplicitUsings>enable</ImplicitUsings>
    <Nullable>enable</Nullable>
    <Configurations>Debug;Release</Configurations>
    <Platforms>AnyCPU</Platforms>
    <EnableDynamicLoading>true</EnableDynamicLoading>
    <RootNamespace>Asv.Drones.Gui.Plugin.Weather</RootNamespace>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Asv.Cfg" Version="$(AsvCommonVersion)" />
    <PackageReference Include="Avalonia" Version="$(AvaloniaVersion)" />
    <PackageReference Include="Avalonia.ReactiveUI" Version="$(AvaloniaVersion)" />
    <PackageReference Include="Avalonia.Xaml.Interactions" Version="$(AvaloniaVersion)" />
    <PackageReference Include="FluentAvaloniaUI" Version="$(FluentAvaloniaUIVersion)" />
    <PackageReference Include="Material.Icons.Avalonia" Version="$(MaterialIconsAvaloniaVersion)" />
    <PackageReference Include="ReactiveUI" Version="$(ReactiveUIVersion)" />
    <PackageReference Include="ReactiveUI.Fody" Version="$(ReactiveUIVersion)" />
    <PackageReference Include="ReactiveUI.Validation" Version="$(ReactiveUIValidationVersion)" />
    <PackageReference Include="System.ComponentModel.Composition" Version="$(CompositionVersion)" />
  </ItemGroup>

  <ItemGroup>
    <EmbeddedResource Update="RS.resx">
      <Generator>PublicResXFileCodeGenerator</Generator>
      <LastGenOutput>RS.Designer.cs</LastGenOutput>
    </EmbeddedResource>
    <EmbeddedResource Update="RS.ru.resx">
      <Generator>PublicResXFileCodeGenerator</Generator>
    </EmbeddedResource>
    <EmbeddedResource Remove="obj\**" />
  </ItemGroup>

  <ItemGroup>
    <Compile Update="RS.Designer.cs">
      <DesignTime>True</DesignTime>
      <AutoGen>True</AutoGen>
      <DependentUpon>RS.resx</DependentUpon>
    </Compile>
    <Compile Update="Shell\Pages\Settings\Weather\WeatherSettingsView.axaml.cs">
      <DependentUpon>WeatherSettingsView.axaml</DependentUpon>
      <SubType>Code</SubType>
    </Compile>
    <Compile Update="Controls\WindIndicator.axaml.cs">
      <DependentUpon>WindIndicator.axaml</DependentUpon>
      <SubType>Code</SubType>
    </Compile>

    <Compile Update="Shell\Pages\Map\Actions\WeatherActionView.axaml.cs">
      <DependentUpon>WeatherView.axaml</DependentUpon>
      <SubType>Code</SubType>
    </Compile>
  </ItemGroup>

  <ItemGroup>
    <ProjectReference Include="..\..\asv-drones\src\Asv.Drones.Gui.Core\Asv.Drones.Gui.Core.csproj" />
  </ItemGroup>

  <ItemGroup>
    <Content Include="$(SolutionDir)Asv.Drones.Gui.Custom.props">
      <Link>Asv.Drones.Gui.Custom.props</Link>
    </Content>
  </ItemGroup>

</Project>
```

Once we've completed the initial steps, we can proceed further.

Our next task involves creating a class that will act as the entry point for our plugin. If we refer to the code provided earlier, we can see that this class is named "WeatherPlugin.cs".

Let's delve into the details of this class!

```csharp
using System.ComponentModel.Composition;
using Asv.Drones.Gui.Core;
using Avalonia;
using Avalonia.Markup.Xaml.Styling;
using Avalonia.Styling;

namespace Asv.Drones.Gui.Plugin.Weather;

[PluginEntryPoint("Weather", CorePlugin.Name)]
[PartCreationPolicy(CreationPolicy.Shared)]
public class WeatherPlugin : IPluginEntryPoint
{
    [ImportingConstructor]
    public WeatherPlugin()
    {
        
    }
    public void Initialize()
    {
        
    }

    public void OnFrameworkInitializationCompleted()
    {
        Application.Current.Styles.Add(new StyleInclude(new Uri("resm:Styles?assembly=Asv.Drones.Gui.Plugin.Weather"))
        {
            Source = new Uri("avares://Asv.Drones.Gui.Plugin.Weather/App.axaml")
        });
    }

    public void OnShutdownRequested()
    {
        
    }
}
```

The WeatherPlugin class implements the IPluginEntryPoint interface and the PluginEntryPoint attribute. This designates the WeatherPlugin class as a plugin entry point. The creation policy for the entry point is always shared.

The plugin project is organized into several folders:
* **Controls:** Contains all custom controls.
* **Service:** Provides all services.
* **Shell:** Includes all shell pages, view-models, and views.

This organization mirrors the structure of the Asv.Drones.Gui solution files. Let's explore the contents of these folders:

1. **Controls:**

   ![Control Folder Files](images/controls-folder-files.png)

   The only file here is **WindIndicator**. This simple custom control adjusts to the given wind angle.

2. **Service:**

   ![Service Folder Files](images/service-folder-files.png)

   This folder implements the weather service class and interface. The Providers folder currently houses two weather providers: Windy and OpenWeatherMap. The service is implemented here for the following reasons: to save and load the last weather data when the weather button is displayed, to download weather data from the selected provider, to save the last selected provider and its API key, and to control the visibility of the action button.

3. **Shell:**

   ![Shell Folder Files](images/shell-folder-files.png)

   There are two views and view-models for the weather. The first is used to add an **action button** to our flight page.

   ![Weather Action Button](images/weather-action-button.png)

   The second is used to display the weather settings in the program settings list.

   ![Weather Settings](images/weather-settings.png)

## Code Explanation

### Weather Action Button

To better understand the action button's functionality, let's examine its view, code-behind, and view-model!

We'll start with the view:

```xaml
<UserControl xmlns="https://github.com/avaloniaui"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
             xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
             xmlns:avalonia="clr-namespace:Material.Icons.Avalonia;assembly=Material.Icons.Avalonia"
             xmlns:weather="clr-namespace:Asv.Drones.Gui.Plugin.Weather"
             mc:Ignorable="d" d:DesignWidth="160" d:DesignHeight="40"
             x:Class="Asv.Drones.Gui.Plugin.Weather.WeatherActionView"
             x:DataType="weather:WeatherActionViewModel"
             IsVisible="{CompiledBinding Visibility}">
    <Design.DataContext>
        <weather:WeatherActionViewModel/>
    </Design.DataContext>
    <Button Command="{CompiledBinding UpdateWeather}" HorizontalAlignment="Center">
        <StackPanel Orientation="Horizontal" Spacing="5">
            <avalonia:MaterialIcon Kind="Temperature"/>
            <TextBlock VerticalAlignment="Center" Text="{CompiledBinding Temperature}"/>
            <weather:WindIndicator Width="20" Height="20" 
                                   Angle="{CompiledBinding WindDirection}" 
                                   Value="{CompiledBinding WindSpeed}"/>
            <TextBlock VerticalAlignment="Center" Text="{CompiledBinding WindSpeedString}"/>
        </StackPanel>
    </Button>
</UserControl>
```
* **Namespaces:** The script starts by defining namespaces. These help the XAML parser understand the meaning of the elements and attributes in your markup. Apart from the standard XAML namespaces, additional namespaces for AvaloniaUI, Material Icons for Avalonia, and the specific Weather plugin are also included.
* **UserControl:** This primary object represented by the top-level UserControl element is the primary object that this XAML defines. UserControl serves as a base class for creating custom, reusable controls.
  * The `x:Class` attribute specifies the code-behind class for this XAML file. Here, the value is `Asv.Drones.Gui.Plugin.Weather.WeatherActionView`.
  * The `x:DataType` attribute indicates the ViewModel that this View binds to, which in this case is `Asv.Drones.Gui.Plugin.Weather.WeatherActionViewModel`.
  * The `IsVisible` attribute binds to the `Visibility` property of `WeatherActionViewModel` and determines the visibility of the UserControl.
* **Design DataContext:** This attribute sets the design-time data context to an instance of `WeatherActionViewModel`. It's primarily used for design-time data binding in visual design tools.
* **Button:** This element establishes a button that triggers the `UpdateWeather` command from the ViewModel upon clicking. The button includes a StackPanel, which aligns several child elements horizontally.
* **StackPanel:** The `StackPanel` has its `Orientation` set to `Horizontal`, aligning its child elements horizontally. It contains an Icon, two TextBlock elements presenting the `Temperature` and `WindSpeed` strings, and a custom `WindIndicator` control.
* **Elements in StackPanel:** The `MaterialIcon`, `TextBlock`, and `WindIndicator` elements inside the `StackPanel` are databound to properties in `WeatherActionViewModel` such as Temperature, WindDirection, and WindSpeed.

Next, let's examine the code-behind of the view:

```csharp
using System.ComponentModel.Composition;
using Asv.Drones.Gui.Core;
using Avalonia.Markup.Xaml;
using Avalonia.ReactiveUI;

namespace Asv.Drones.Gui.Plugin.Weather;

[ExportView(typeof(WeatherActionViewModel))]
[PartCreationPolicy(CreationPolicy.NonShared)]
public partial class WeatherActionView : ReactiveUserControl<WeatherActionViewModel>
{
    public WeatherActionView()
    {
        InitializeComponent();
    }

    private void InitializeComponent()
    {
        AvaloniaXamlLoader.Load(this);
    }
}
```

* **Namespaces:**
  * `System.ComponentModel.Composition` is a namespace that comprises types used for creating extensible applications in the Managed Extensibility Framework (MEF).
  * `Asv.Drones.Gui.Core`, `Avalonia.Markup.Xaml`, and `Avalonia.ReactiveUI` are specific to the libraries utilized the application.
* **Annotations:**
  * The `[ExportView(typeof(WeatherActionViewModel))]` attribute signifies that this class offers an exported view for the WeatherActionViewModel, which is used for dependency injection. It's a specific attribute of the `Asv.Drones.Gui.Core` library.
  * `[PartCreationPolicy(CreationPolicy.NonShared)]` attribute, part of MEF, denotes that a new instance of `WeatherActionView` is initiated each time it's required.
* **Class Declaration:**
  * The `WeatherActionView` class inherits from `ReactiveUserControl<WeatherActionViewModel>`, a class from the ReactiveUI library that supports a reactive programming model. `WeatherActionViewModel` is the view model that this view binds to.
* **Methods:**
  * In the constructor (`public WeatherActionView()`), the `InitializeComponent()` method is called. This method uses `AvaloniaXamlLoader.Load(this)` to load the relevant Avalonia XAML for the current control.

Next, let's dive into the view-model:

```csharp
using System.ComponentModel.Composition;
using System.Windows.Input;
using Asv.Common;
using Asv.Drones.Gui.Core;
using Avalonia;
using Avalonia.Controls;
using Avalonia.Markup.Xaml.Styling;
using DynamicData.Binding;
using ReactiveUI;
using ReactiveUI.Fody.Helpers;

namespace Asv.Drones.Gui.Plugin.Weather;

[Export(FlightPageViewModel.UriString,typeof(IMapAction))]
[PartCreationPolicy(CreationPolicy.NonShared)]
public class WeatherActionViewModel : MapActionBase
{
    private readonly IWeatherService _weatherService;
    private readonly ILocalizationService _localizationService;

    public WeatherActionViewModel() : base("asv:shell.page.map.action.weather")
    {
        if (Design.IsDesignMode)
        {
            Application.Current.Styles.Add(new StyleInclude(new Uri("resm:Styles?assembly=Asv.Drones.Gui.Plugin.Weather"))
            {
                Source = new Uri("avares://Asv.Drones.Gui.Plugin.Weather/App.axaml")
            });
            
            Visibility = true;
            
            CurrentWeatherData = new WeatherData
            {
                WindSpeed = 8,
                WindDirection = 123,
                Temperature = 22.25
            };

            WindDirection = CurrentWeatherData.WindDirection;
            WindSpeedString = $"{CurrentWeatherData.WindSpeed} m/s";
            Temperature = $"{CurrentWeatherData.Temperature} K";
        }
    }
    
    [ImportingConstructor]
    public WeatherActionViewModel(IWeatherService weatherService, ILocalizationService localizationService) : this()
    {
        _weatherService = weatherService;
        _localizationService = localizationService;

        _weatherService.LastWeatherData
            .Subscribe(_ => CurrentWeatherData = _)
            .DisposeItWith(Disposable);
        
        _weatherService.Visibility
            .Subscribe(_ => Visibility = _)
            .DisposeItWith(Disposable);
    }
    
    public ICommand UpdateWeather { get; set; }
    
    [Reactive]
    public WeatherData CurrentWeatherData { get; set; }
    
    [Reactive]
    public string WindSpeedString { get; set; }
    
    [Reactive]
    public double WindSpeed { get; set; }

    [Reactive]
    public double WindDirection { get; set; }
    
    [Reactive]
    public string Temperature { get; set; }

    [Reactive]
    public bool Visibility { get; set; }
    
    public async Task UpdateWeatherImpl(GeoPoint location)
    {
        CurrentWeatherData = await _weatherService.GetWeatherData(location);
    }

    
    public override IMapAction Init(IMap context)
    {
        base.Init(context);
        UpdateWeather = ReactiveCommand.CreateFromTask(
            () => UpdateWeatherImpl(context.Center)).DisposeItWith(Disposable);
        
        this.WhenPropertyChanged(_ => _.CurrentWeatherData)
            .Subscribe(_ =>
            {
                if (CurrentWeatherData != null)
                {
                    WindDirection = CurrentWeatherData.WindDirection;
                    WindSpeed = CurrentWeatherData.WindSpeed;
                    WindSpeedString = _localizationService.Velocity.FromSiToStringWithUnits(CurrentWeatherData.WindSpeed);
                    Temperature = _localizationService.Temperature.FromSiToStringWithUnits(CurrentWeatherData.Temperature);

                    _weatherService.LastWeatherData.OnNext(CurrentWeatherData);
                }
            })
            .DisposeItWith(Disposable);
        
        return this;
    }
}
```
* **Dependencies:**
  * The class employs MEF (Managed Extensibility Framework) as a form of dependency injection, as indicated by the Export and ImportingConstructor attributes. The dependencies in this class include `IWeatherService` and `ILocalizationService`.
* **Design Mode Behavior:**
  * The default constructor features a block of code that executes conditionally when the application operates in Design Mode, which is usually when it is being edited in a UI designer of an IDE. This block applies specific styling to the application and sets some default values.
* **ViewModel Properties:**
  * The ViewModel exposes several properties:
    * `UpdateWeather` is a command bound to a UI action (likely a button click event) that invokes the `UpdateWeatherImpl` method.
    * `CurrentWeatherData`, `WindSpeedString`, `WindSpeed`, `WindDirection`, `Temperature`, and `Visibility` are all Reactively bound properties. The ReactiveUI framework is used here for declarative UI updates. The `[Reactive]` attribute instructs ReactiveUI to raise PropertyChanged events whenever the value of these properties changes.
* **Initialization:**
  * The `Init` method establishes the command, the map center, and subscribes to the `CurrentWeatherData` property's changes. Accordingly, it updates the UI (Wind direction, Wind speed, Temperature, and triggers the `LastWeatherData` event) whenever `CurrentWeatherData` changes. Upon initializing it, the method returns an instance of the class.

### Weather Settings

Here's the view:

```xaml
<UserControl xmlns="https://github.com/avaloniaui"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
             xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
             xmlns:weather="clr-namespace:Asv.Drones.Gui.Plugin.Weather"
             xmlns:core="clr-namespace:Asv.Drones.Gui.Core;assembly=Asv.Drones.Gui.Core"
             mc:Ignorable="d" d:DesignWidth="800" d:DesignHeight="450"
             x:Class="Asv.Drones.Gui.Plugin.Weather.WeatherSettingsView"
             x:CompileBindings="True"
             x:DataType="weather:WeatherSettingsViewModel">
    <Design.DataContext>
        <weather:WeatherSettingsViewModel/>
    </Design.DataContext>
    <core:OptionsDisplayItem Header="{x:Static weather:RS.WeatherSettingsView_Header}"
                             Icon="{Binding WeatherIcon}"
                             Description="{x:Static weather:RS.WeatherSettingsView_Description}"
                             Expands="True"
                             IsExpanded="False">
        <core:OptionsDisplayItem.Content>
            <StackPanel Spacing="8">
                <core:OptionsDisplayItem Header="{x:Static weather:RS.WeatherSettingsView_WeatherSwitch_Header}" 
                                         Icon="{Binding WeatherSwitchIcon}"
                                         Description="{x:Static weather:RS.WeatherSettingsView_WeatherSwitch_Description}">
                    <core:OptionsDisplayItem.ActionButton>
                        <ToggleButton Content="{CompiledBinding VisibilityButton}" IsChecked="{CompiledBinding Visibility}"/>
                    </core:OptionsDisplayItem.ActionButton>
                </core:OptionsDisplayItem>
                <core:OptionsDisplayItem Header="{x:Static weather:RS.WeatherSettingsView_WeatherProvider_Header}"
                                         Icon="{Binding WeatherIcon}"
                                         Description="{x:Static weather:RS.WeatherSettingsView_WeatherProvider_Description}">
                    <core:OptionsDisplayItem.ActionButton>
                        <ComboBox Width="180" ItemsSource="{CompiledBinding WeatherProviders}" 
                                  SelectedItem="{CompiledBinding CurrentWeatherProvider}">
                            <ComboBox.ItemTemplate>
                                <DataTemplate>
                                    <TextBlock Text="{CompiledBinding Name}"/>
                                </DataTemplate>
                            </ComboBox.ItemTemplate>
                        </ComboBox>   
                    </core:OptionsDisplayItem.ActionButton>
                </core:OptionsDisplayItem>
                <core:OptionsDisplayItem Header="{x:Static weather:RS.WeatherSettingsView_WeatherApiKey_Header}"
                                         Icon="{Binding WeatherApiKeyIcon}"
                                         Description="{x:Static weather:RS.WeatherSettingsView_WeatherApiKey_Description}">
                    <core:OptionsDisplayItem.ActionButton>
                        <TextBox Watermark="Enter your api key" Width="180" Text="{CompiledBinding CurrentWeatherProviderApiKey}"/>
                    </core:OptionsDisplayItem.ActionButton>
                </core:OptionsDisplayItem>
            </StackPanel>
        </core:OptionsDisplayItem.Content>
    </core:OptionsDisplayItem>
</UserControl>
```

1. The highest level describes a `UserControl`, which is a custom, reusable control composed of other controls.
2. The namespaces declared at the top map XML namespaces to CLR (Common Language Runtime) namespaces. This mapping allows the XML markup to use types in the corresponding CLR namespaces.
3. The `x:Class` attribute specifies the CLR namespace and class name for the code-behind class of the XAML file.
4. `x:DataType` represents the type of object to be used for data binding in this view. Here, it represents a ViewModel type from the weather CLR namespace.
5. The `Design.DataContext` element assigns a design-time DataContext, which is the ViewModel instance the IDE's designer uses to render the XAML at design-time.
6. `core:OptionsDisplayItem` is a custom control used in the application. Various properties, such as `Header`, `Icon`, `Description`, `Expands`, and `IsExpanded`, are set.
7. Within the `OptionsDisplayItem`, a `StackPanel` allows for the arrangement of multiple `core:OptionsDisplayItem` elements in a stack, either horizontally or vertically. A vertical arrangement is utilized here (which is the default).
8. Inside each `core:OptionsDisplayItem`, there are `ToggleButton`, `ComboBox`, and `TextBox` controls used for different functionalities such as toggling visibility, selecting from multiple options, and entering text, respectively.
9. `{CompiledBinding}` is a markup extension that recommends a binding to be compiled for improved performance.
10. `{x:Static}` refers to a static property. For instance, `{x:Static weather:RS.WeatherSettingsView_Header}` refers to the static `WeatherSettingsView_Header` property on the `RS` class in the `weather` namespace.
11. The `ComboBox.ItemTemplate` property defines a `DataTemplate` that describes how data objects should be displayed. In this case, the `TextBlock` element is employed to display the `Name` property.

Next, let's explore the code-behind:

```csharp
using System.ComponentModel.Composition;
using Asv.Drones.Gui.Core;
using Avalonia;
using Avalonia.Controls;
using Avalonia.Markup.Xaml;
using Avalonia.ReactiveUI;

namespace Asv.Drones.Gui.Plugin.Weather;

[ExportView(typeof(WeatherSettingsViewModel))]
[PartCreationPolicy(CreationPolicy.NonShared)]
public partial class WeatherSettingsView : ReactiveUserControl<WeatherSettingsViewModel>
{
    public WeatherSettingsView()
    {
        InitializeComponent();
    }
    
    private void InitializeComponent()
    {
        AvaloniaXamlLoader.Load(this);
    }
}
```

* The `using` statements at the top import required references.
* The namespace `Asv.Drones.Gui.Plugin.Weather` contains this class. Namespaces help organize your code.
* The `[ExportView(typeof(WeatherSettingsViewModel))]` attribute marks this class for export via MEF (Managed Extensibility Framework). In this context, it's paired with a ViewModel type `WeatherSettingsViewModel` through the `ExportView` attribute. The framework will then inject instances of this view when necessary within your application.
* The `[PartCreationPolicy(CreationPolicy.NonShared)]` attribute indicates that new instances of this class should be created every time the class needs to be injected or consumed within the program. The `CreationPolicy.NonShared` specifies that MEF will not cache and reuse the instances of this part. Instead, it will always create a new instance of `WeatherSettingsView` whenever it is requested.
* `ReactiveUserControl<WeatherSettingsViewModel>` is the base class that `WeatherSettingsView` inherits from. Avalonia.ReactiveUI leverages a specific UI programming paradigm inspired by functional reactive programming. In this situation, `WeatherSettingsViewModel` is the associated ViewModel type.
* The `InitializeComponent` method is where the Avalonia UI gets loaded via the `AvaloniaXamlLoader.Load(this)` line. The layout, controls, and styles for this component are expected to be defined in the corresponding XAML file.

Lastly, let's consider the shell's ViewModel:

```csharp
using System;
using System.Collections.Generic;
using System.ComponentModel.Composition;
using Asv.Common;
using Asv.Drones.Gui.Core;
using DynamicData.Binding;
using Material.Icons;
using ReactiveUI;
using ReactiveUI.Fody.Helpers;

namespace Asv.Drones.Gui.Plugin.Weather;

[Export(typeof(ISettingsPart))]
[PartCreationPolicy(CreationPolicy.NonShared)]
public class WeatherSettingsViewModel : SettingsPartBase
{
    private static readonly Uri Uri = new(SettingsPartBase.Uri, "weather");
    private readonly IWeatherService _weatherService;

    public WeatherSettingsViewModel() : base(Uri)
    {
        
    }

    [ImportingConstructor]
    public WeatherSettingsViewModel(IWeatherService weatherService) : this()
    {
        _weatherService = weatherService;
        
        _weatherService.Visibility.Subscribe(_ => Visibility = _).DisposeItWith(Disposable);
        
        this.WhenAnyValue(_ => _.Visibility)
            .Subscribe(_weatherService.Visibility)
            .DisposeItWith(Disposable);
        
        this.WhenValueChanged(_ => _.Visibility)
            .Subscribe(_ =>
            {
                VisibilityButton = _
                    ? RS.WeatherSettingsView_WeatherSwitch_Button_Hide
                    : RS.WeatherSettingsView_WeatherSwitch_Button_Show;
            })
            .DisposeItWith(Disposable);
        
        _weatherService.CurrentWeatherProvider.Subscribe(_ => CurrentWeatherProvider = _).DisposeItWith(Disposable);
        
        this.WhenAnyValue(_ => _.CurrentWeatherProvider)
            .Subscribe(_weatherService.CurrentWeatherProvider)
            .DisposeItWith(Disposable);
        
        _weatherService.CurrentWeatherProviderApiKey.Subscribe(_ => CurrentWeatherProviderApiKey = _).DisposeItWith(Disposable);
        
        this.WhenAnyValue(_ => _.CurrentWeatherProviderApiKey)
            .Subscribe(_weatherService.CurrentWeatherProviderApiKey)
            .DisposeItWith(Disposable);
    }

    public override int Order => 4;
    
    [Reactive]
    public bool Visibility { get; set; }

    [Reactive]
    public string VisibilityButton { get; set; }
    
    [Reactive]
    public IWeatherProviderBase CurrentWeatherProvider { get; set; }
    
    [Reactive]
    public string CurrentWeatherProviderApiKey { get; set; }

    public IEnumerable<IWeatherProviderBase> WeatherProviders => _weatherService.WeatherProviders;
    public string WeatherIcon => MaterialIconDataProvider.GetData(MaterialIconKind.WeatherPartlyCloudy);
    public string WeatherApiKeyIcon => MaterialIconDataProvider.GetData(MaterialIconKind.Key);
    public string WeatherSwitchIcon => MaterialIconDataProvider.GetData(MaterialIconKind.Visibility);
}
```

The `WeatherSettingsViewModel` class extends from `SettingsPartBase` and implements the `ISettingsPart` interface, serving as a ViewModel in an MVVM (Model-View-ViewModel) pattern. It's tasked with defining and managing the state and the operations related to the application's weather settings.

Here are several key points:

1. **MEF and Dependency Injection:** The class employs the Managed Extensibility Framework (MEF), evident from the `Export` and `ImportingConstructor` attributes, as a form of Dependency Injection.
2. **IWeatherService:** This interface delivers methods and properties related to the app's weather functionality. The `WeatherSettingsViewModel` interacts with this service to manipulate and present data.
3. **ReactiveUI and Reactive Properties:** The class uses ReactiveUI, an MVVM framework, for reactive programming to streamline state management. The `[Reactive]` attribute is applied to define properties (`Visibility`, `VisibilityButton`, `CurrentWeatherProvider`, and `CurrentWeatherProviderApiKey`) that will automatically notify the UI of any changes.
4. **Subscriptions:** They enable the ViewModel to subscribe to changes in certain properties and execute corresponding actions. For example, one subscription responds to changes in the `Visibility` property and adjusts the text of `VisibilityButton` accordingly.
5. **Properties:** `WeatherProviders` returns available weather providers, and `WeatherIcon`, `WeatherApiKeyIcon`, and `WeatherSwitchIcon` methods fetch data to display specific icons in the UI.
6. Overall, this class acts as a bridge between the view and the model, manipulating data given by `IWeatherService` and presenting it to the UI in an engaging manner. Changes in the reactive properties will reflect in the UI, offering real-time interactivity.

### Weather service class and interface

#### Interface 

```csharp
using System.Collections.Generic;
using System.Threading.Tasks;
using Asv.Common;

namespace Asv.Drones.Gui.Plugin.Weather;

public interface IWeatherService
{
    public IRxEditableValue<bool> Visibility { get; }
    public IEnumerable<IWeatherProviderBase> WeatherProviders { get; }
    public IRxEditableValue<string> CurrentWeatherProviderApiKey { get; }
    public IRxEditableValue<IWeatherProviderBase> CurrentWeatherProvider { get; }

    public IRxEditableValue<WeatherData> LastWeatherData { get; }
    public Task<WeatherData> GetWeatherData(GeoPoint location);
}
```

#### Class

```csharp
using System;
using System.Collections.Generic;
using System.ComponentModel.Composition;
using System.Linq;
using System.Threading.Tasks;
using Asv.Cfg;
using Asv.Common;
using Asv.Drones.Gui.Core;

namespace Asv.Drones.Gui.Plugin.Weather;

public class WeatherServiceConfig
{
    public bool Visibility { get; set; }
    public string CurrentProviderName { get; set; }
    public Dictionary<string, string> ProvidersApiKeys { get; set; } = new();
    
    public WeatherData LastWeatherData { get; set; }
}

[Export(typeof(IWeatherService))]
[PartCreationPolicy(CreationPolicy.Shared)]
public class WeatherService : ServiceWithConfigBase<WeatherServiceConfig>, IWeatherService
{
    private readonly IEnumerable<IWeatherProviderBase> _weatherProviders;

    [ImportingConstructor]
    public WeatherService(IConfiguration cfg, [ImportMany] IEnumerable<IWeatherProviderBase> weatherProviders) : base(cfg)
    {
        if (cfg == null) throw new ArgumentNullException(nameof(cfg));
        
        var visibilityFromConfig = InternalGetConfig(_ => _.Visibility);
        
        Visibility = new RxValue<bool>(visibilityFromConfig).DisposeItWith(Disposable);
        
        Visibility.Subscribe(SetVisibility).DisposeItWith(Disposable);
        
        var weatherProviderFromConfig = InternalGetConfig(_ => _.CurrentProviderName);

        if (weatherProviderFromConfig.IsNullOrWhiteSpace())
        {
            CurrentWeatherProvider = new RxValue<IWeatherProviderBase>(
                    weatherProviders.FirstOrDefault())
                .DisposeItWith(Disposable);
        }
        else
        {
            CurrentWeatherProvider = new RxValue<IWeatherProviderBase>(
                    weatherProviders.SingleOrDefault(_ => _.Name == weatherProviderFromConfig))
                .DisposeItWith(Disposable);
        }
        
        CurrentWeatherProvider.Subscribe(SetCurrentProvider).DisposeItWith(Disposable);
        
        var weatherProviderApiKeyFromConfig = 
            InternalGetConfig(_ =>
            {
                if (_.ProvidersApiKeys == null) _.ProvidersApiKeys = new();
                if (_.ProvidersApiKeys.TryGetValue(CurrentWeatherProvider.Value.Name, out var __))
                {
                    return __;
                }
                return "";
            });

        CurrentWeatherProviderApiKey = new RxValue<string>(weatherProviderApiKeyFromConfig)
            .DisposeItWith(Disposable);
        
        CurrentWeatherProviderApiKey.Subscribe(SetCurrentProviderApiKey).DisposeItWith(Disposable);
        
        var lastWeatherDataFromConfig = InternalGetConfig(_ => _.LastWeatherData);
        
        LastWeatherData = new RxValue<WeatherData>(lastWeatherDataFromConfig ?? new WeatherData()).DisposeItWith(Disposable);
        
        LastWeatherData.Subscribe(SetLastWeatherData).DisposeItWith(Disposable);
        
        _weatherProviders = weatherProviders;
    }
    
    public IRxEditableValue<bool> Visibility { get; }

    public IRxEditableValue<WeatherData> LastWeatherData { get; }
    
    public IRxEditableValue<IWeatherProviderBase> CurrentWeatherProvider { get; }

    public IRxEditableValue<string> CurrentWeatherProviderApiKey { get; }
    
    public IEnumerable<IWeatherProviderBase> WeatherProviders => _weatherProviders;
    
    private void SetVisibility(bool visibility)
    {
        InternalSaveConfig(_ => _.Visibility = visibility);
    }

    private async void SetCurrentProvider(IWeatherProviderBase provider)
    {
        if (CurrentWeatherProviderApiKey != null)
        {
            var apiKey = InternalGetConfig(_ =>
            {
                if (_.ProvidersApiKeys == null) 
                    _.ProvidersApiKeys = new();
                
                if (_.ProvidersApiKeys.TryGetValue(provider.Name, out var __))
                {
                    return __;
                }
                return "";
            });
            
            CurrentWeatherProviderApiKey.OnNext(apiKey);

            CurrentWeatherProvider.Value.ApiKey = apiKey;
        }
        
        InternalSaveConfig(_ => _.CurrentProviderName = provider.Name);
    }

    private void SetCurrentProviderApiKey(string key)
    {
        CurrentWeatherProvider.Value.ApiKey = key;
        
        InternalSaveConfig(_ => 
            _.ProvidersApiKeys[CurrentWeatherProvider.Value.Name] = key);
    }
    
    private void SetLastWeatherData(WeatherData data)
    {
        InternalSaveConfig(_ =>_.LastWeatherData = data);
    }
    
    public async Task<WeatherData> GetWeatherData(GeoPoint location)
    {
        return await CurrentWeatherProvider.Value.GetWeatherData(location);
    }
}
```

The `WeatherService` class employs the MEF (Managed Extensibility Framework) for plugin management, as indicated by the `Export` and `ImportingConstructor` attributes. The MEF facilitates loose coupling between the main application and its extensions or plugins.

The `WeatherServiceConfig` class outlines the configuration for the `WeatherService`, such as the visibility setting, the current weather provider, the last weather data, and a dictionary for storing API keys of various weather providers.

Within the `WeatherService` class:

* The class inherits `ServiceWithConfigBase<WeatherServiceConfig>`, using a configuration of the `WeatherServiceConfig` type.
* It contains an instance of `IEnumerable<IWeatherProviderBase>`, representing multiple weather data providers.
* Its constructor imports the configuration and the list of weather providers. The configuration is used to access and adjust properties like visibility, current weather provider, API key, and the last weather data.
* The `IRxEditableValue<T>` instances denote reactive properties. Any change in these properties triggers a defined action. For instance, a modification in the `Visibility` property triggers the `SetVisibility` method.
* Every time there's a shift in a reactive property, the corresponding method (like `SetVisibility`, `SetCurrentProvider`, `SetCurrentProviderApiKey`, `SetLastWeatherData`) is invoked, which in turn updates the configuration accordingly.
* The `GetWeatherData(GeoPoint location)` method fetches weather data for a specific location.

### WeatherData and IWeatherProviderBase

```csharp
using System.Threading.Tasks;
using Asv.Common;

namespace Asv.Drones.Gui.Plugin.Weather;

public class WeatherData
{
    public double WindSpeed { get; set; }
    public double WindDirection { get; set; }
    public double Temperature { get; set; }
}

public interface IWeatherProviderBase
{
    string ApiKey { get; set; }
    string Name { get; }

    Task<WeatherData> GetWeatherData(GeoPoint location);
    Task<WeatherData> GetWeatherData(double latitude, double longitude);
}
```

The class at the top, `WeatherData`, includes three properties: `WindSpeed`, `WindDirection`, and `Temperature`. Each of these properties is of the `double` type. Presumably, this class is intended to store weather-related data.

The `IWeatherProviderBase` interface is declared at the bottom. It outlines a property `ApiKey`, a read-only property `Name`, and two `GetWeatherData` methods. The `ApiKey` is presumably used for authenticating with the weather data provider's API, while `Name` likely represents the name of the weather provider.

The first `GetWeatherData` method takes a `GeoPoint` object as a parameter, whereas the second `GetWeatherData` method takes two `double` parameters for latitude and longitude. Both methods return a `Task<WeatherData>`, suggesting these are asynchronous methods that fetch `WeatherData` from a source, and are expected to be used with the `async/await` structure.

One note to make is that the `GeoPoint` type is not defined in this code snippet; it appears to be part of the `Asv.Common` namespace.

Any class intending to provide weather data would implement this interface, permitting different providers to be easily swapped out without the rest of the codebase needing to know where the data comes from. By using an interface, concrete implementations can have distinct behaviors yet still conform to a contract defined by the interface, thus promoting code reusability and modularity.
