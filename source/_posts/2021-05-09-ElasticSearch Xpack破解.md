---
title: ElasticSearch Xpack破解
date: 2021-05-09 10:28
---

## 环境说明

推荐使用 <https://github.com/117503445/elasticsearch-deploy> 进行快速的部署，就不用进行下述的手动破解了，部署完 把 <https://github.com/117503445/elasticsearch-deploy/blob/main/license.json> 传到 ES 上直接就激活了。

## 破解

### 正常激活流程

用户要先去官网申请/购买个 License，形如 <https://github.com/117503445/elasticsearch-deploy/blob/main/license.json>，然后把这个 License 上传到 ES 的后台。ES 会检查这个 License 有没有被篡改，没被篡改的话再根据这个 License 的信息进行激活。

### 破解思路

license 中有个 signature 字段，ES 会根据这个字段判断 License 是否被篡改。只要取消 ES 的这个判断逻辑，就可以随便篡改 License，达到激活的目的了。

### 具体步骤

我是基于 官方 ES Docker 镜像 7.12.0 版本进行破解的。
关键路径

- /use/local/bin/docker-entrypoint.sh Docker 镜像启动脚本
- /usr/share/elasticsearch ES安装位置

因为涉及 Java 编译，先装上相关依赖。`yum install java-devel`，保证有 javac 这个命令。

然后

```bash
cd /root
mkdir crack
cd crack
```

创建 2 个文件

/root/crack/LicenseVerifier.java

```java
package org.elasticsearch.license;

import java.nio.*;
import org.elasticsearch.common.bytes.*;
import java.security.*;
import java.util.*;
import org.elasticsearch.common.xcontent.*;
import org.apache.lucene.util.*;
import org.elasticsearch.core.internal.io.*;
import java.io.*;

public class LicenseVerifier
{
    public static boolean verifyLicense(final License license, final byte[] publicKeyData) {
        return true;
    }
    
    public static boolean verifyLicense(final License license) {
        return true;
    }
}
```

/root/crack/XPackBuild.java

```java
package org.elasticsearch.xpack.core;

import org.elasticsearch.common.io.*;
import java.net.*;
import org.elasticsearch.common.*;
import java.nio.file.*;
import java.io.*;
import java.util.jar.*;

public class XPackBuild
{
    public static final XPackBuild CURRENT;
    private String shortHash;
    private String date;
    
    @SuppressForbidden(reason = "looks up path of xpack.jar directly")
    static Path getElasticsearchCodebase() {
        final URL url = XPackBuild.class.getProtectionDomain().getCodeSource().getLocation();
        try {
            return PathUtils.get(url.toURI());
        }
        catch (URISyntaxException bogus) {
            throw new RuntimeException(bogus);
        }
    }
    
    XPackBuild(final String shortHash, final String date) {
        this.shortHash = shortHash;
        this.date = date;
    }
    
    public String shortHash() {
        return this.shortHash;
    }
    
    public String date() {
        return this.date;
    }
    
    static {
        final Path path = getElasticsearchCodebase();
        String shortHash = null;
        String date = null;
        Label_0109: {
            shortHash = "Unknown";
            date = "Unknown";
        }
        CURRENT = new XPackBuild(shortHash, date);
    }
}

```

这 2 个代码是对 /usr/share/elasticsearch/modules/x-pack-core/x-pack-core-7.12.0.jar 进行解包以后反编译出来的，然后删掉了验证相关的代码。具体怎么做的可以看引用的博客。

有了这 2 个 Java 文件以后，就可以进行编译了。

```sh
ES_HOME="/usr/share/elasticsearch"
ES_JAR=$(cd $ES_HOME && ls lib/elasticsearch-[0-9]*.jar)
ESCORE_JAR=$(cd $ES_HOME && ls lib/elasticsearch-core-*.jar)
LUCENE_JAR=$(cd $ES_HOME && ls lib/lucene-core-*.jar)
XPACK_JAR=$(cd $ES_HOME && ls modules/x-pack-core/x-pack-core-*.jar)

javac -cp "${ES_HOME}/${ES_JAR}:${ES_HOME}/${LUCENE_JAR}:${ES_HOME}/${XPACK_JAR}:${ES_HOME}/${ESCORE_JAR}" LicenseVerifier.java
javac -cp "${ES_HOME}/${ES_JAR}:${ES_HOME}/${LUCENE_JAR}:${ES_HOME}/${XPACK_JAR}:${ES_HOME}/${ESCORE_JAR}" XPackBuild.java
```

执行 这段脚本，就可以得到 2 个 Java 代码 对应的 class 文件。

然后 把 x-pack-core-7.12.0.jar 解压，替换掉 2 个 class 文件，再打包再丢回去，破解就成功了。

## Ref

[Elasticsearch 7.x 白金版](https://knner.wang/2019/11/29/elastic-7-platinum-crack.html)
