---
title: "Simplify Configuration Management in Go with the 'conf' Package"
date: "2023-10-25"
description: "Learn how to manage configuration in your Go applications with the 'conf' package and customize it using flags."
tags: [go,conf]
categories: ["technology"]
---


The "conf" package is designed to make configuration management in Go straightforward and flexible. It allows you to define your application's configuration as a struct and then parse it for both environment variables and command-line arguments. This approach makes it easy to handle different configuration sources, and the package provides a variety of customization options through struct tags.



First, you need to install the "conf" package:

```bash
go get github.com/ardanlabs/conf/v3
```



Define a struct that represents your application's configuration. You can use struct tags to specify default values, environment variable names, and other options.


```go

type appConfig struct {
    Port       int    `conf:"default:8080,env:APP_PORT"`
    Host       string `conf:"default:localhost,env:APP_HOST"`
    DebugMode  bool   `conf:"default:false"`
    LogFile    string `conf:"default:/var/log/myapp.log"`
    Timeout    time.Duration `conf:"default:30s"`
}

```

Now, you can use the `conf.Parse` function to parse the configuration from environment variables and command line arguments.

```go
func main() {
    var cfg appConfig
    help, err := conf.Parse("APP", &cfg)
    if err != nil {
        if errors.Is(err, conf.ErrHelpWanted) {
            fmt.Println(help)
            return
        }
        fmt.Printf("Error parsing configuration: %v\n", err)
        return
    }

    // Your application logic using the cfg object
    fmt.Printf("Port: %d\n", cfg.Port)
    fmt.Printf("Host: %s\n", cfg.Host)
    fmt.Printf("Debug Mode: %v\n", cfg.DebugMode)
    fmt.Printf("Log File: %s\n", cfg.LogFile)
    fmt.Printf("Timeout: %s\n", cfg.Timeout)
}
```

Running this program would print this output:

```bash
➜ go run main.go 
Port: 8080
Host: localhost
Debug Mode: false
Log File: /var/log/myapp.log
Timeout: 30s
```


If you want to support command line arguments in addition to environment variables, you can simply add the command line arguments after the flags. For example, if you run your application as follows:

```bash
➜ go run main.go --port=8081 --debug-mode
Port: 8081
Host: localhost
Debug Mode: true
Log File: /var/log/myapp.log
Timeout: 30s
``` 

You can also display the usage of your application's configuration for users. This is helpful for users to understand the available configuration options.

```bash
➜ go run main.go -h
Usage: main [options] [arguments]

OPTIONS
  --port/$APP_APP_PORT          <int>       (default: 8080)
  --host/$APP_APP_HOST          <string>    (default: localhost)
  --debug-mode/$APP_DEBUG_MODE  <bool>      (default: false)
  --log-file/$APP_LOG_FILE      <string>    (default: /var/log/myapp.log)
  --timeout/$APP_TIMEOUT        <duration>  (default: 30s)
  --help/-h                     
  display this help message

```

If you want to include version information for your application, you can use the `Version` type. First, modify your `appConfig` struct to include the `Version` type.


```go
type AppConfig struct {
    // Existing fields...
    Version conf.Version
}
```

Then, set the version and description:

```go
cfg := AppConfig{
    // Existing configuration...
    Version: conf.Version{
        Build: "v1.0.0",
        Desc:  "Your Application Description",
    },
}
```


## Customization Options

The "conf" package offers a range of customization options through struct tags, allowing you to fine-tune how configuration fields are handled. Here's a closer look at these customization flags:

### 1. `default`

The `default` flag allows you to specify a default value for a configuration field. This default value is used when no value is provided in environment variables or command-line arguments. For example, you can set a default port value as follows:

```go
Port int `conf:"default:8080"`
```

### 2. `env`

The `env` flag gives you the ability to override the default environment variable name associated with a configuration field. This is particularly useful when you want to use specific environment variable names that align with your application's naming conventions. For instance, you can specify a custom environment variable name for the host:

```go
Host string `conf:"env:APP_HOST"`
```

### 3. `flag`

The `flag` flag allows you to override the default flag name for a field. This is handy when you want to provide a custom command-line flag name for a configuration option. For example, you can change the flag name for enabling debug mode:

```go
DebugMode bool `conf:"flag:debug"`
```

### 4. `short`

The `short` flag is used to denote a shorthand option for the flag. You can specify a single character as a shorthand flag for a configuration field, making it quicker to set from the command line. For instance, you can define a shorthand flag 'l' for the log file:

```go
LogFile string `conf:"short:l"`
```

### 5. `noprint`

The `noprint` flag is particularly helpful when you have fields that are for internal use and don't need to be displayed to end-users in the usage information. By tagging a field with `noprint`, you indicate that it should not be included in the display string. Here's an example:

```go
SecretToken string `conf:"noprint"`
```

### 6. `mask`

Sometimes, you might have fields containing sensitive data like passwords. The `mask` flag comes to the rescue by including the field in the display string but masking out the actual value. This provides security for sensitive data. You can tag a password field as follows:

```go
Password string `conf:"mask"`
```

### 7. `required`

The `required` flag is used to denote that a value must be provided for a field. If a value is not provided through environment variables or command-line arguments, an error will be raised, indicating that the field is mandatory. For instance, marking the API key as required:

```go
APIKey string `conf:"required"`
```

### 8. `help`

The `help` flag allows you to provide a description for the field. This description is invaluable for generating informative usage information for your application's configuration. Users can refer to this description to understand the purpose of each configuration option. Here's an example:

```go
Timeout int `conf:"help:Connection timeout in seconds"`
```

By using these customization options, you can create a highly adaptable configuration system tailored to your application's specific needs. The flags allow you to provide context, security, and usability for your configuration fields.

### Example: Applying Flags to Configuration

To illustrate how these flags work in practice, let's consider an example. We have an `AppConfig` struct that includes various fields with different flags applied:

```go
type AppConfig struct {
    Port int `conf:"default:8080"`
    Host string `conf:"env:APP_HOST"`
    DebugMode bool `conf:"flag:debug"`
    LogFile string `conf:"short:l"`
    SecretToken string `conf:"noprint"`
    Password string `conf:"mask"`
    APIKey string `conf:"required"`
    Timeout int `conf:"help:Connection timeout in seconds"`
}
```

In this example, the `Port` field has a default value, the `Host` field overrides the environment variable name, the `DebugMode` field overrides the flag name, and so on.

With this configuration in place, you can use the `conf.Parse` function to parse the configuration values. The configured flags and their customizations will be applied during the parsing process.

## Conclusion

The "conf" package simplifies configuration management in Go by providing support for environment variables and command-line arguments. With the flexibility of customization flags, you can fine-tune your application's configuration to suit your specific needs, ensuring that your application runs smoothly in different environments.

To learn more about the "conf" package and its advanced features, you can refer to the official documentation on [go.dev](https://pkg.go.dev/github.com/ardanlabs/conf/v3).
