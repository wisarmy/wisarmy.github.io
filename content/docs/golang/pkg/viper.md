# 前言

版本 v1.7.0

更详细文档请移步至[官方文档](https://github.com/spf13/viper#what-is-viper)


## 读取文件
```go
viper.SetConfigName("config") // 文件名
viper.SetConfigType("yaml") // 扩展名
viper.AddConfigPath(".")   // 添加路径
viper.ReadInConfig() // 读取
```
## 写入文件
```go
// 写入当前配置路径文件 (by 'viper.AddConfigPath()' and 'viper.SetConfigName()')
viper.WriteConfig()	// 覆盖
viper.SafeWriteConfig()	// 不覆盖
// 写入指定的配置文件
viper.WriteConfigAs("/path/to/my/.config") // 覆盖
viper.SafeWriteConfigAs("/path/to/my/.config") // 不覆盖
```
## 运行时重读
无需重启服务
```go
viper.WatchConfig()	// 告诉viper，监听配置，更改时重新载入
// 配置变更事件
viper.OnConfigChange(func(e fsnotify.Event) {
	fmt.Println("Config file changed:", e.Name)
    // 做一些有趣的事情，like, viper.Unmarshal to update config
})
```
## 从io.Reader读取
```go
viper.SetConfigType("toml")	// 设置配置类型
var configExample = `debug = true`	// 定义配置内容
viper.ReadConfig(bytes.NewBufferString(configExample))	// bytes.Buffer实现了io.Reader接口
fmt.Println(viper.GetBool("debug")) // true
```
## 设置配置
```go
viper.SetDefault("debug", false)	// 设置默认值
viper.SetDefault("jwt", map[string]string{"aglo": "HS256", "secret": ""})	// 更复杂结构
viper.Set("debug", true)	// 有时我们需要在程序逻辑中更改或命令行flag来控制
```
## 别名
多个键引用一个值, 如果配置名称需变更，无需改动业务程序
```go
viper.RegisterAlias("glad", "pleased")
viper.Set("glad", "happy")
viper.GetString("pleased")	// happy
```
## with flag
```go
// 使用标准库 flag
flag.String("config", "app.toml", "config filename")
flag.Parse()
// 设置pflag
pflag.CommandLine.AddGoFlagSet(flag.CommandLine)	// 若以上使用pflag，无需此操作
viper.BindPFlags(pflag.CommandLine)
viper.GetString("config")
```



