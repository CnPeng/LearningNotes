# 1. 4-检查手机是否root

## 1.1. 检测代码

下面的代码是当前项目中正在使用的代码（其他同事测试过，但我并没有实测。）

```java
public class CheckRoot {
    public static boolean isDeviceRooted() {
        if (checkDeviceDebuggable()) {
            return true;
        }
        //check buildTags
        if (checkSuperuserApk()) {
            return true;
        }//Superuser.apk
        //if (checkRootPathSU()){return true;}//find su in some path
        //if (checkRootWhichSU()){return true;}//find su use 'which'
        if (checkBusybox()) {
            return true;
        }
        //find su use 'which'
        if (checkAccessRootData()) {
            return true;
        }
        //find su use 'which'
        if (checkGetRootAuth()) {
            return true;
        }//exec su

        return false;
    }

    public static boolean checkDeviceDebuggable() {
        String buildTags = android.os.Build.TAGS;
        if (buildTags != null && buildTags.contains("test-keys")) {
            //GxtLog.i( "buildTags=" + buildTags);
            return true;
        }
        return false;
    }

    public static boolean checkSuperuserApk() {
        try {
            File file = new File("/system/app/Superuser.apk");
            if (file.exists()) {
                //GxtLog.i( "/system/app/Superuser.apk exist");
                return true;
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
        return false;
    }

    public static synchronized boolean checkGetRootAuth() {
        Process process = null;
        DataOutputStream os = null;
        try {
            //GxtLog.i( "to exec su");
            process = Runtime.getRuntime().exec("su");
            os = new DataOutputStream(process.getOutputStream());
            os.writeBytes("exit\n");
            os.flush();
            int exitValue = process.waitFor();
            //GxtLog.i( "exitValue=" + exitValue);
            if (exitValue == 0) {
                return true;
            } else {
                return false;
            }
        } catch (Exception e) {
            //GxtLog.i( "Unexpected error - Here is what I know: " + e.getMessage());
            return false;
        } finally {
            try {
                if (os != null) {
                    os.close();
                }
                if (process != null) {
                    process.destroy();
                }
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }

    public static synchronized boolean checkBusybox() {
        try {
            //GxtLog.i( "to exec busybox df");
            String[] strCmd = new String[]{"busybox", "df"};
            ArrayList<String> execResult = executeCommand(strCmd);
            if (execResult != null) {
                //GxtLog.i( "execResult=" + execResult.toString());
                return true;
            } else {
                //GxtLog.i( "execResult=null");
                return false;
            }
        } catch (Exception e) {
            //GxtLog.i( "Unexpected error - Here is what I know: " + e.getMessage());
            return false;
        }
    }

    public static ArrayList<String> executeCommand(String[] shellCmd) {
        String line = null;
        ArrayList<String> fullResponse = new ArrayList<String>();
        Process localProcess = null;
        try {
            //GxtLog.i( "to shell exec which for find su :");
            localProcess = Runtime.getRuntime().exec(shellCmd);
        } catch (Exception e) {
            return null;
        }
        BufferedWriter out = new BufferedWriter(new OutputStreamWriter(localProcess.getOutputStream()));
        BufferedReader in = new BufferedReader(new InputStreamReader(localProcess.getInputStream()));
        try {
            while ((line = in.readLine()) != null) {
                //GxtLog.i( "–> Line received: " + line);
                fullResponse.add(line);
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
        //GxtLog.i( "–> Full response was: " + fullResponse);
        return fullResponse;
    }

    public static synchronized boolean checkAccessRootData() {
        try {
            //GxtLog.i( "to write /data");
            String fileContent = "test_ok";
            Boolean writeFlag = writeFile("/data/su_test", fileContent);
            if (writeFlag) {
                //GxtLog.i( "write ok");
            } else {
                //GxtLog.i( "write failed");
            }

            //GxtLog.i( "to read /data");
            String strRead = readFile("/data/su_test");
            //GxtLog.i( "strRead=" + strRead);
            return fileContent.equals(strRead);
        } catch (Exception e) {
            //GxtLog.i( "Unexpected error - Here is what I know: " + e.getMessage());
            return false;
        }
    }

    //写文件
    public static Boolean writeFile(String fileName, String message) {
        try {
            FileOutputStream fout = new FileOutputStream(fileName);
            byte[] bytes = message.getBytes();
            fout.write(bytes);
            fout.close();
            return true;
        } catch (Exception e) {
            // e.printStackTrace();
            return false;
        }
    }

    //读文件
    public static String readFile(String fileName) {
        File file = new File(fileName);
        try {
            FileInputStream fis = new FileInputStream(file);
            byte[] bytes = new byte[1024];
            ByteArrayOutputStream bos = new ByteArrayOutputStream();
            int len;
            while ((len = fis.read(bytes)) > 0) {
                bos.write(bytes, 0, len);
            }
            String result = new String(bos.toByteArray());
            //GxtLog.i( result);
            return result;
        } catch (Exception e) {
            //e.printStackTrace();
            return null;
        }
    }
}
```

## 1.2. 其他参考

> 下面的内容仅做摘录，也没有实测过。

[原文链接：《Android 检测手机是否被Root》](https://blog.csdn.net/qq_32886769/article/details/112848474)

```java
public class CheckSysUtils {
    public static boolean isDeviceRooted() {
        return checkRootMethod1() || checkRootMethod2() || checkRootMethod3();
    }

    //查看系统是否测试版
    private static boolean checkRootMethod1() {
        String buildTags = android.os.Build.TAGS;
        return buildTags != null && buildTags.contains("test-keys");
    }

    //通过检测指定目录下是否存在su程序来检测运行环境是否为Root设备
    private static boolean checkRootMethod2() {
        String[] paths = {"/system/app/Superuser.apk", "/sbin/su", "/system/bin/su", "/system/xbin/su", "/data/local/xbin/su", "/data/local/bin/su", "/system/sd/xbin/su",
                "/system/bin/failsafe/su", "/data/local/su", "/su/bin/su"};
        for (String path : paths) {
            if (new File(path).exists()) return true;
        }
        return false;
    }

    //通过which命令检测系统PATH变量指定的路径下是否存在su程序来检测运行环境是否为Root设备
    private static boolean checkRootMethod3() {
        Process process = null;
        try {
            process = Runtime.getRuntime().exec(new String[]{"/system/xbin/which", "su"});
            BufferedReader in = new BufferedReader(new InputStreamReader(process.getInputStream()));
            if (in.readLine() != null) return true;
            return false;
        } catch (Throwable t) {
            return false;
        } finally {
            if (process != null) process.destroy();
        }
    }
}
```
