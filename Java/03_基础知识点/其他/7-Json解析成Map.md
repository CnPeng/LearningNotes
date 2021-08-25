[JSON串转换成Map以及如何转换到对应的数据类型](https://bijian1013.iteye.com/blog/2219050)

下面的示例使用了 fastjson —— （在不使用 fastjson 时可以参考其解析思路，核心处理是一致的）

```java
package com.study;  
  
import java.util.ArrayList;  
import java.util.HashMap;  
import java.util.Iterator;  
import java.util.List;  
import java.util.Map;  
import java.util.Map.Entry;  
  
import com.alibaba.fastjson.JSONArray;  
import com.alibaba.fastjson.JSONObject;  
import com.alibaba.fastjson.serializer.SerializerFeature;  
  
public class JsonUtil {  
      
    private static List<Map<String, Object>> json2List(Object json) {  
        JSONArray jsonArr = (JSONArray) json;  
        List<Map<String, Object>> arrList = new ArrayList<Map<String, Object>>();  
        for (int i = 0; i < jsonArr.size(); ++i) {  
            arrList.add(strJson2Map(jsonArr.getString(i)));  
        }  
        return arrList;  
    }  
  
    public static Map<String, Object> strJson2Map(String json) {  
        JSONObject jsonObject = JSONObject.parseObject(json);  
        Map<String, Object> resMap = new HashMap<String, Object>();  
        Iterator<Entry<String, Object>> it = jsonObject.entrySet().iterator();  
        while (it.hasNext()) {  
            Map.Entry<String, Object> param = (Map.Entry<String, Object>) it.next();  
            if (param.getValue() instanceof JSONObject) {  
                resMap.put(param.getKey(), strJson2Map(param.getValue().toString()));  
            } else if (param.getValue() instanceof JSONArray) {  
                resMap.put(param.getKey(), json2List(param.getValue()));  
            } else {  
                resMap.put(param.getKey(), JSONObject.toJSONString(param.getValue(), SerializerFeature.WriteClassName));  
            }  
        }  
        return resMap;  
    }  
}  
```