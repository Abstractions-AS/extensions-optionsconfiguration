
# Introduction

[![NuGet version (Abstractions.Extensions.AspNetCore.OptionsConfiguration)](https://img.shields.io/nuget/v/Abstractions.Extensions.AspNetCore.OptionsConfiguration)](https://www.nuget.org/packages/Abstractions.Extensions.AspNetCore.OptionsConfiguration/)

This is a small extension library meant to be used by library authors creating libraries for asp.net core.
The library will ensure that required options classes are configured by the consuming application

# Example
Given that you are creating the library 'MyLibrary' which requires the following options class to be configured

```csharp
public class MyLibraryOptions
{
	/// <summary>
	/// The endpoint to use when calling the service
	/// </summary>
	public string Endpoint { get; set; } = "https://default.endpoint";

	/// <summary>
	/// Required. Your api key.
	/// </summary>
	[Required]
	public string ApiKey { get; set; }
}
```

The MyLibrary might define the following extensions class:

```csharp
public static class MyLibraryExtensions
{
	public static IServiceCollection AddMyLibrary(this IServiceCollection services)
	{
		
		services.TryAddSingleton<IMyService, MyService>();

		// This line ensures that the given options type has been configured.
		// If it has not been configured, the library will automatically look for 
		// configuration sections named either 'MyLibrary' or 'MyLibraryOptions' 
		// and configure them automatically.
		//
		// The call will also validate data annotations on the options class.
		services.AddFallbackConfiguration<MyLibraryOptions>();

		// By default, the library will throw an exception if the options have not been
		// configured and no options can be configured automatically.
		//
		// If you want the library to configure autmatically, but not throw an exception
		// set required to false:
		services.AddFallbackConfiguration<MyLibraryOptions>(required: false);
		    
		return services;
	}
}