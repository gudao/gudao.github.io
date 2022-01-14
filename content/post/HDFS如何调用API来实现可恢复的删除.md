---
title: "HDFS如何调用API来实现可恢复的删除"
date: 2013-08-23
draft: false
tags: [hdfs,可恢复删除,recoverable delete]
categories: [Hadoop]
---

![](/Content/upload/Img20130823/s_n0wdh2rr_qc1.png) 
    之前写过《[Hadoop删除数据恢复配置](http://www.front2end.cn/blog/Hadoop-deleted-data-recovery-configuration.html)》的文章中提到了如何配置HDFS使其可以在一定时间段内可恢复数据（需要调用shell的rmr命令才能实现），今天着重说如何调用API来实现可恢复的删除操作。

    拿HadoopThriftServer.java（hadoop-1.2.1\src\contrib\thriftfs\src\java\org\apache\hadoop\thriftfs\HadoopThriftServer.java）文件来举例，文件中实现了delete操作如下：

    


<pre>    /**
     * Delete a file/directory
     */
    public boolean rm(Pathname path, boolean recursive) 
                          throws ThriftIOException {
      try {
        now = now();
        HadoopThriftHandler.LOG.debug(&#34;rm: &#34; + path +
                                     &#34; recursive: &#34; + recursive);
        boolean ret = fs.delete(new Path(path.pathname), recursive);
        HadoopThriftHandler.LOG.debug(&#34;rm: &#34; + path);
        return ret;
      } catch (IOException e) {
        throw new ThriftIOException(e.getMessage());
      }
    }
</pre>

其中fs是FileSystem的实例，直接调用其delete方法就是彻底删除。如果想实现可恢复的删除必须用到Trash对象，下面上修改以后的函数来说明：




<pre> /**
     * Delete a file/directory
     */
    public boolean rm(Pathname path, boolean recursive,boolean skipTrash) 
                          throws ThriftIOException {
      try {
        now = now();
        HadoopThriftHandler.LOG.debug(&#34;rm: &#34; + path +
                                     &#34; recursive: &#34; + recursive);
        Path src=new Path(path.pathname);
       
        if(!skipTrash) {
            try {
      	      Trash trashTmp = new Trash(fs,conf);
              if (trashTmp.moveToTrash(src)) {
                System.out.println(&#34;Moved to trash: &#34; + src);
                return true;
              }
            } catch (IOException e) {
              Exception cause = (Exception) e.getCause();
              String msg = &#34;&#34;;
              if(cause != null) {
                msg = cause.getLocalizedMessage();
              }
              System.err.println(&#34;Problem with Trash.&#34; + msg +&#34;. Consider using -skipTrash option&#34;);        
              throw e;
            }
          }
        boolean ret = fs.delete(new Path(path.pathname), recursive);
        
        HadoopThriftHandler.LOG.debug(&#34;rm: &#34; + path);
        return ret;
      } catch (IOException e) {
        throw new ThriftIOException(e.getMessage());
      }
    }</pre>



添加一个是否跳过回收站参数，即可判断是否进行可恢复删除。

同时需要对应的更新hadoopfs.thrift，以便客户端的程序能够成功调用。


 
- - -
 