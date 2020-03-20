```
gradlew -q moudle名称:dependencies
```

如果提示：
```
./gradlew: Permission denied
```
则先执行：
```
//修改 gradlew的读取权限为可读可写可执行
chmod +x gradlew
```