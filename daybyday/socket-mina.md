# MINA

* [Mina系列文章索引](http://my.oschina.net/ielts0909/blog/92716)

## mina

### IoBuffer和ByteBuffer

This is a replacement for ByteBuffer. Please refer to ByteBuffer documentation for preliminary usage. MINA does not use NIO ByteBuffer directly for two reasons:

* It doesn't provide useful getters and putters such as fill, get/putString, and get/putAsciiInt\(\) enough. 
* It is difficult to write variable-length data due to its fixed capacity 

