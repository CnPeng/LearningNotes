# 1. 2-防止被hook

[原文：《Android hook检测（安全方向）》](https://www.jianshu.com/p/137ca0a371d6)

## 1.1. 什么是hook
所谓hook技术，就是通过一段代码(反射、代理)侵入到App启动过程中，在原本执行的代码前插入其它的功能。比如：通过hook技术，上传登陆页面的账号密码等。

## 1.2. 防止被 hook

对于主流 hook 框架（Xposed、Cydia Substrate），通常有以下三种方式来检测一个App是否被hook:

* 安装目录中是否存在hook工具
* 存储中是否存在hook安装文件
* 运行栈中是否存在hook相关类

> 注意：**Xposed和Cydia Substrate，都是在手机 root 得前提下才能用**。所以，还有必要检测手机是否被 root , root 了就可以认为存在风险。

### 1.2.1. 查找设备安装目录中是否存在 hook 工具

```java
private static boolean findHookAppName(Context context) {
    PackageManager packageManager = context.getPackageManager();
    List<ApplicationInfo> applicationInfoList = packageManager
            .getInstalledApplications(PackageManager.GET_META_DATA);
    for (ApplicationInfo applicationInfo : applicationInfoList) {
        if (applicationInfo.packageName.equals("de.robv.android.xposed.installer")) {
            Log.wtf("HookDetection", "Xposed found on the system.");
            return true;
        }
        if (applicationInfo.packageName.equals("com.saurik.substrate")) {
            Log.wtf("HookDetection", "Substrate found on the system.");
            return true;
        }
    }
    return false;
}
```

### 1.2.2. 查找设备存储中是否存在hook安装文件

```java
private static boolean findHookAppFile() {
    try {
        Set<String> libraries = new HashSet<String>();
        String mapsFilename = "/proc/" + android.os.Process.myPid() + "/maps";
        BufferedReader reader = new BufferedReader(new FileReader(mapsFilename));
        String line;
        while ((line = reader.readLine()) != null) {
            if (line.endsWith(".so") || line.endsWith(".jar")) {
                int n = line.lastIndexOf(" ");
                libraries.add(line.substring(n + 1));
            }
        }
        reader.close();
        for (String library : libraries) {
            if (library.contains("com.saurik.substrate")) {
                Log.wtf("HookDetection", "Substrate shared object found: " + library);
                return true;
            }
            if (library.contains("XposedBridge.jar")) {
                Log.wtf("HookDetection", "Xposed JAR found: " + library);
                return true;
            }
        }
    } catch (Exception e) {
        Log.wtf("HookDetection", e.toString());
    }
    return false;
}
```

### 1.2.3. 查找程序运行的栈中是否存在hook相关类

```java
private static boolean findHookStack() {
    try {
        throw new Exception("findhook");
    } catch (Exception e) {
        // 读取栈信息
        // for(StackTraceElement stackTraceElement : e.getStackTrace()) {
        // Log.wtf("HookDetection", stackTraceElement.getClassName() + "->"+
        // stackTraceElement.getMethodName());
        // }
        int zygoteInitCallCount = 0;
        for (StackTraceElement stackTraceElement : e.getStackTrace()) {
            if (stackTraceElement.getClassName().equals("com.android.internal.os.ZygoteInit")) {
                zygoteInitCallCount++;
                if (zygoteInitCallCount == 2) {
                    Log.wtf("HookDetection", "Substrate is active on the device.");
                    return true;
                }
            }
            if (stackTraceElement.getClassName().equals("com.saurik.substrate.MS$2")
                    && stackTraceElement.getMethodName().equals("invoked")) {
                Log.wtf("HookDetection", "A method on the stack trace has been hooked using Substrate.");
                return true;
            }
            if (stackTraceElement.getClassName().equals("de.robv.android.xposed.XposedBridge")
                    && stackTraceElement.getMethodName().equals("main")) {
                Log.wtf("HookDetection", "Xposed is active on the device.");
                return true;
            }
            if (stackTraceElement.getClassName().equals("de.robv.android.xposed.XposedBridge")
                    && stackTraceElement.getMethodName().equals("handleHookedMethod")) {
                Log.wtf("HookDetection", "A method on the stack trace has been hooked using Xposed.");
                return true;
            }
        }
    }
    return false;
}
```

### 1.2.4. 综合判断 （true为hook）

```java
    public static boolean isHook(Context context) {
        if (findHookAppName(context) || findHookAppFile() || findHookStack()) {
            return true;
        }
        return false;
    }
```

## 1.3. 参考：

* [《Android Java层的hook检测（Cydia Substrate或者Xposed框架）》](http://blog.csdn.net/u012131859/article/details/51612641)

* [《Android Hook 简介》](https://blog.csdn.net/yin1031468524/article/details/63254757)

## 1.4. 完整代码

```java

/**
 * CnPeng: 2021/8/11 15:48
 * 功用：hook 检查
 * 其他：检测本机是否有安装 hook 工具，如果有，则不允许继续使用 app
 * 参考：https://www.jianshu.com/p/137ca0a371d6
 */
public class DetectHookUtil {
    private static final DetectHookUtil ourInstance = new DetectHookUtil();

    public static DetectHookUtil getInstance() {
        return ourInstance;
    }

    private DetectHookUtil() {
    }

    /**
     * CnPeng:2021/8/11 15:54 设备中是否存在 hook 工具
     *
     * @return boolean true-存在hook工具，false-不存在hook工具
     */
    public boolean isHookAppExist(Context context) {
        return findHookAppName(context) || findHookAppFile() || findHookStack();
    }

    /**
     * CnPeng:2021/8/11 15:52 根据 app 名称检测是否有安装 hook 工具
     */
    private boolean findHookAppName(Context context) {
        PackageManager packageManager = context.getPackageManager();
        List<ApplicationInfo> applicationInfoList = packageManager
                .getInstalledApplications(PackageManager.GET_META_DATA);

        for (ApplicationInfo applicationInfo : applicationInfoList) {
            if (applicationInfo.packageName.equals("de.robv.android.xposed.installer")) {
                GxtLog.wtf("Xposed found on the system.");
                return true;
            }
            if (applicationInfo.packageName.equals("com.saurik.substrate")) {
                GxtLog.wtf("Substrate found on the system.");
                return true;
            }
        }
        return false;
    }

    /**
     * CnPeng:2021/8/11 15:53 检测设备的存储中是否有 hook 安装文件
     */
    private boolean findHookAppFile() {
        try {
            Set<String> libraries = new HashSet<String>();
            String mapsFilename = "/proc/" + android.os.Process.myPid() + "/maps";
            BufferedReader reader = new BufferedReader(new FileReader(mapsFilename));
            String line;
            while ((line = reader.readLine()) != null) {
                if (line.endsWith(".so") || line.endsWith(".jar")) {
                    int n = line.lastIndexOf(" ");
                    libraries.add(line.substring(n + 1));
                }
            }
            reader.close();
            for (String library : libraries) {
                if (library.contains("com.saurik.substrate")) {
                    Log.wtf("HookDetection", "Substrate shared object found: " + library);
                    return true;
                }
                if (library.contains("XposedBridge.jar")) {
                    Log.wtf("HookDetection", "Xposed JAR found: " + library);
                    return true;
                }
            }
        } catch (Exception e) {
            Log.wtf("HookDetection", e.toString());
        }
        return false;
    }

    /**
     * CnPeng:2021/8/11 15:53 检测是否有 hook 相关的类信息（栈信息）
     */
    private boolean findHookStack() {
        try {
            throw new Exception("findhook");
        } catch (Exception e) {

            // 读取栈信息
            // for(StackTraceElement stackTraceElement : e.getStackTrace()) {
            // Log.wtf("HookDetection", stackTraceElement.getClassName() + "->"+
            // stackTraceElement.getMethodName());
            // }

            int zygoteInitCallCount = 0;
            for (StackTraceElement stackTraceElement : e.getStackTrace()) {
                if (stackTraceElement.getClassName().equals("com.android.internal.os.ZygoteInit")) {
                    zygoteInitCallCount++;
                    if (zygoteInitCallCount == 2) {
                        Log.wtf("HookDetection", "Substrate is active on the device.");
                        return true;
                    }
                }
                if (stackTraceElement.getClassName().equals("com.saurik.substrate.MS$2")
                        && stackTraceElement.getMethodName().equals("invoked")) {
                    Log.wtf("HookDetection", "A method on the stack trace has been hooked using Substrate.");
                    return true;
                }
                if (stackTraceElement.getClassName().equals("de.robv.android.xposed.XposedBridge")
                        && stackTraceElement.getMethodName().equals("main")) {
                    Log.wtf("HookDetection", "Xposed is active on the device.");
                    return true;
                }
                if (stackTraceElement.getClassName().equals("de.robv.android.xposed.XposedBridge")
                        && stackTraceElement.getMethodName().equals("handleHookedMethod")) {
                    Log.wtf("HookDetection", "A method on the stack trace has been hooked using Xposed.");
                    return true;
                }
            }
        }
        return false;
    }
}
```