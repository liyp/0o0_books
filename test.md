
# XXX

## test
{\"aps\": {\"alert\":{\"title\":\"Game\ Request\",\"body\":\"Bob\ wants\ to\ play\ poker\",\"action-loc-key\":\"PLAY\"},\"badge\":5,},\"acme1\":\"bar\",\"acme2\":[\"bang\",\"whiz\"]}

{\"aps\":{\"alert\":{\"title\":\"Game-Request\",\"body\":\"Bob-wants-to-play-poker\",\"action-loc-key\":\"PLAY\"},\"badge\":5},\"acme1\":\"bar\",\"acme2\":[\"bang\",\"whiz\"]}

{\"aps\":{\"alert\":{\"title\":\"Game-Request\",\"body\":\"Bob-wants-to-play\ poker\"}}}


## 云账号

accountID: 7013  
"cloudUserName":"test@tplinkyun.com"  
"cloudPassword":"pass12345"  
  

INSERT INTO `push_app_task` (`account_id`, `event`, `request_content`, `task_date`, `status_code`, `msg_id`, `type`, `task_id`) VALUES (7013, 'deviceMessage', '{\"content\":\"192%2E168%2E1%2E10%20%CA%B9%D3%C3%B9%DC%C0%ED%D4%B1%C3%DC%C2%EB%B5%C7%C2%BC%C1%CB%C2%B7%D3%C9%C6%F7%B9%DC%C0%ED%BD%E7%C3%E6%A1%A3\",\"time\":\"1422334088\",\"displayType\":1,\"msgId\":\"3_1422334088_50bd5fed41ee_0\",\"mac\":\"50BD5FED41EE\",\"encodeType\":\"gb2312\",\"eventType\":\"network\",\"deviceId\":\"3A124C7E3347773B5071224FA243FBB906BC3B8F\",\"retainedMessageBar\":0}', '2015-1-27 16:02:32', 1, '3_1422334088_50bd5fed41ee_0', NULL, NULL);

'{"aps":{"alert":"192%2E168%2E1%2E10%20%CA%B9%D3%C3%B9%DC%C0%ED%D4%B1%C3%DC%C2%EB%B5%C7%C2%BC%C1%CB%C2%B7%D3%C9%C6%F7%B9%DC%C0%ED%BD%E7%C3%E6%A1%A3"},"time":"1422334088","displayType":1,"mac":"50BD5FED41EE","encodeType":"gb2312","msgType":"network","deviceId":"3A124C7E3347773B5071224FA243FBB906BC3B8F"}'


INSERT INTO `push_app_task` (`account_id`, `event`, `request_content`, `task_date`, `status_code`, `msg_id`, `type`, `task_id`) VALUES (7013, 'newPlugIn', '{\"content\":\"版本更新，建议升级使用\",\"time\":\"1422518252793\",\"displayType\":0,\"msgId\":\"1_1422518252793_703971_1\",\"mac\":\"50BD5FED1B82\",\"eventType\":\"newPlugIn\",\"deviceId\":\"91F8F438BAE077DE66E3A019BA69122D19CA7B81\",\"retainedMessageBar\":0}', '2015-1-29 15:57:32', 1, NULL, NULL, NULL);

'{"aps":{"alert":"版本更新，建议升级使用"},"time":"1422518252793","displayType":0,"mac":"50BD5FED1B82","msgType":"newPlugIn","deviceId":"91F8F438BAE077DE66E3A019BA69122D19CA7B81"}'


INSERT INTO `push_app_task` (`account_id`, `event`, `request_content`, `task_date`, `status_code`, `msg_id`, `type`, `task_id`) VALUES (7013, 'newFirmware', '{\"content\":\"\",\"time\":\"1423707725106\",\"displayType\":7,\"msgId\":\"1_1423707725106_634034_12\",\"mac\":\"50BD5FED41EE\",\"eventType\":\"newFirmware\",\"deviceId\":\"3A124C7E3347773B5071224FA243FBB906BC3B8F\",\"retainedMessageBar\":1}', '2015-2-12 10:22:05', -1, NULL, NULL, NULL);

2


### 字符集

[由web程序出现乱码开始挖掘(Bom头、字符集与乱码）](http://www.cnblogs.com/chengmo/archive/2010/10/30/1864004.html)

> 什么是bom头？

bom全称是：byte order mark，汉语意思是标记字节顺序码。只是出现在：unicode字符集中，只有unicode字符集，存储时候，要求指定编码，如果不指定，windows还会用默认的：ANSI读取。常见的bom头是：

  UTF-8 ║ EF BB BF 
  UTF-16LE ║ FF FE (小尾）
  UTF-16BE ║ FE FF （大尾）
  UTF-32LE ║ FF FE 00 00 
  UTF-32BE ║ 00 00 FE FF

> unicode与utf-8 、utf-16 utf-32是什么关系？

unicode（统一码、万国码、单一码）是一种字符集，Unicode是国际组织制定的可以容纳世界上所有文字和符号的字符编码方案。Unicode用数字0-0x10FFFF来映射这些字符，最多可以容纳1114112个字符，或者说有1114112个码位。UTF-8、UTF-16、UTF-32都是将数字转换到程序数据的编码方案。在Unicode中：汉字“字”对应的数字是23383。我们可以用：UTF-8、UTF-16、UTF-32表示这个数字，将数字23383存储在计算机中。UTF-8对应是：0xE6, 0xB1, 0x89(3个字节）,UTF-16对应是：0x6c49（2个字节）,UTF-32对应是：0x6c49（4个字节）。utf-8,utf-16,utf-32是unicode码一种实现形式，都是属于unicode编码。

> unicode编码特点是什么？

unicode编码特点是，它定义了编码方式和存储实现方式。编码方式就是上面说的可以用，utf-8…utf-32表示，而存储实现方式，无论那种编码都知道了文件头(bom)。因此，可以通过这个特殊头来判断存储的文本文件使用那种字符集编码。 