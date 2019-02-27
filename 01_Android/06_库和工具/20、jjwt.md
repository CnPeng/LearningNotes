https://github.com/jwtk/jjwt

安卓项目中依赖该库的时候，需要使用如下配置：
```
    implementation 'io.jsonwebtoken:jjwt-api:0.10.2'
    runtimeOnly 'io.jsonwebtoken:jjwt-impl:0.10.2'
    runtimeOnly('io.jsonwebtoken:jjwt-orgjson:0.10.2') {
        exclude group: 'org.json', module: 'json' //provided by Android natively
    }
    // Uncomment the next line if you want to use RSASSA-PSS (PS256, PS384, PS512) algorithms:
//    runtime 'org.bouncycastle:bcprov-jdk15on:1.60'
```

jjwt ReadMe.md 文档中的依赖方式已经过时了