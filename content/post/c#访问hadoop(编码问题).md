---
title: "c#访问hadoop(编码问题)"
date: 2012-06-13
draft: false
tags: [hadoop,thirft,c#,utf-8,iso-8859-1]
categories: [Hadoop]
---

环境说明：

        客户端：win7下用C#写的winform

        服务器：ubuntu10下Hadoop Thrift

        问题：客户端连接Hadoop上传、下载二进制文件如图片、视频均乱码。

这个问题整整让我研究了两天，下面说说我分析的思路。

   ** 一、害人的UTF-8** 

    hadoop官方提供的thrift是用utf8编码读取文件的，我自然也最先使用这个编码来处理C#的文件。然而发现文件乱码，于是先测试一下是不是我客户端的问题，下面这个测试说明了utf8处理二进制文件是不可行的：




<pre>            FileStream readfs = new FileStream(&#34;f:\\a.jpg&#34;, FileMode.Open, FileAccess.Read);

            FileStream writefs = new FileStream(&#34;f:\\b.jpg&#34;, FileMode.Create, FileAccess.Write);

            byte[] fileBuffer = new byte[1024];
            int bytesRead;

            while ((bytesRead = readfs.Read(fileBuffer, 0, fileBuffer.Length)) &gt; 0)
            {

                string tem = Encoding.UTF8.GetString(fileBuffer, 0, fileBuffer.Length);

                byte[] wrbuf=Encoding.UTF8.GetBytes(tem);
                
                writefs.Write(wrbuf, 0, wrbuf.Length);

            }

            readfs.Close();
            writefs.Close();</pre>

    测试很简单，意思就是说，将一个图片用流的方式读出来，将流转换成utf8的字符串，然后再将字符串用utf8转换成byte数组存成文件。结果是文件被损坏了。原因是utf8编码遇到一个识别不了的byte（如：byte值255），变用一个类似问号的字符（对应byte值为：239、191、189）代替，从而使原本为255的转换为了三个值。


    解决方法为，先将流文件用iso-8859-1编码转换一下或者直接用iso-8859-1就可以了。任然用上面的例子，修改如下：




<pre>            FileStream readfs = new FileStream(&#34;f:\\a.jpg&#34;, FileMode.Open, FileAccess.Read);

            FileStream writefs = new FileStream(&#34;f:\\b.jpg&#34;, FileMode.Create, FileAccess.Write);

            byte[] fileBuffer = new byte[1024];
            int bytesRead;

            while ((bytesRead = readfs.Read(fileBuffer, 0, fileBuffer.Length)) &gt; 0)
            {
                byte[] buf = Encoding.Convert(Encoding.GetEncoding(&#34;iso-8859-1&#34;), Encoding.UTF8, fileBuffer);

                string tem = Encoding.UTF8.GetString(buf, 0, buf.Length);

                byte[] wrbuf=Encoding.UTF8.GetBytes(tem);
                wrbuf=Encoding.Convert(Encoding.UTF8,Encoding.GetEncoding(&#34;iso-8859-1&#34;), wrbuf);

                writefs.Write(wrbuf, 0, wrbuf.Length);

            }

            readfs.Close();
            writefs.Close();
</pre>



**二、迷惑仍然不解** 

    知道了utf8的问题，便在客户端进行了转换，把转换后的utf8编码发给服务器端，结果还是不行。于是猜测是thrift在其中做了手脚(因为ThriftHadoopFileSystem.cs的write方法的参数data是string类型)，查看thrift的源码发现，TProtocol中WriteString方法果然是用了utf8将data做了转码。于是将write方法给为了 bool write(ThriftHandle handle, byte[] data)参数为byte[]类型，这下没有了字符串应该没问题了吧，我晕，结果还是不行。

三、排除法

    客户端的错误的可能已经排除了，于是猜测问题是不是出在服务器端，难道java的utf8就可以读取二进制？先用hadoop的shell上传一张图片，用客户端下载下来，果然文件的二进制已经被篡改了。猜测服务器端的java读取文件有问题，光猜测还是不行的，用java在本地实验如下：




<pre>InputStream in = null;
	        
	        try {
	            System.out.println(&#34;以字节为单位读取文件内容，一次读多个字节：&#34;);
	            byte[] tempbytes = new byte[100];
	            int byteread = 0;
	            in = new FileInputStream(fileName);
	            while ((byteread = in.read(tempbytes)) != -1) {
	            String heihei=	new String(tempbytes, 0, byteread, &#34;UTF-8&#34;);
	            byte[] tmp2 = heihei.getBytes(&#34;UTF-8&#34;);
	            
	          System.out.write(tempbytes, 0, byteread);
	            }
	        } catch (Exception e1) {
	            e1.printStackTrace();
	        } finally {
	            if (in != null) {
	                try {
	                    in.close();
	                } catch (IOException e1) {
	                }
	            }
	        }</pre>

其中tmp2的值和tempbytes的值果然不一样。我就晕了，为什么hadoop官方要用utf8？表示不解的同时用了ISO-8859-1做了测试（代码不贴了把UTF-8替换即可）结果没问题。看来只能把服务器端代码做修改了。 


**四、大功告成，替换hadoop-thriftfs-1.0.3.jar** 

    本地java测试后，只能替换java的服务器端了，于是找到hadoop-thriftfs-1.0.3.jar的源文件HadoopThriftServer.java将read、write方法的UTF-8替换为了ISO-8859-1，重新编译打包jar，（在hadoop根目录/bin文件夹下）替换后，重启start_thrift_server.sh，终于大功告成！上传、下载图片、视频都没问题了！ 

**五、成功之后的思考** 

    虽然问题修改成功了，我觉得我是侥幸的。因为我还有一问题不明，看下面我的客户端代码即知： 

  


<pre>public bool Create(ThriftHadoopFileSystem.Client client, string localPath, string path)
       {
           bool result = false;
           if (client != null)
           {
               ThriftHandle th = null;
               FileStream fs = null;
               try
               {
                   //th = client.create(new Pathname() { pathname = path });
                   th = client.createFile(new Pathname() { pathname = path }, 1, false, 1024, 1, 1024*1024*64);
                   
                   UTF8Encoding utf8 = new UTF8Encoding(false,true);

                   fs = new FileStream(localPath, FileMode.Open, FileAccess.Read);

                   byte[] fileBuffer = new byte[1024 * 1024];	// 每次传1MB
                   int bytesRead;
                   while ((bytesRead = fs.Read(fileBuffer, 0, fileBuffer.Length)) &gt; 0)
                   {
                       byte[] realBuffer = new byte[bytesRead];
                       Array.Copy(fileBuffer, realBuffer, bytesRead);

                       byte[] buf = Encoding.Convert(Encoding.GetEncoding(&#34;iso-8859-1&#34;), utf8, realBuffer);

                       client.write(th,buf);
                       Array.Clear(fileBuffer, 0, fileBuffer.Length);
                   }
                   result = true;
               }
               catch (Exception ee)
               {
                   throw ee;
               }
               finally
               {
                   if (th != null)
                       client.close(th);
                   if (fs != null)
                       fs.Close();
               }
           }

           return result;
       }

public bool Open(string path,string savePath,long fileLength)
       {
           TBufferedTransport tsport = null;
           ThriftHadoopFileSystem.Client client = Connect(out tsport);
           bool result = false;
           if (client != null)
           {
               ThriftHandle th = client.open(new Pathname() { pathname = path });
               
               // 创建文件流
               FileStream fs = new FileStream(savePath, FileMode.Create, FileAccess.Write);
               long totalBytes = 0;
               int readLength=1024*1024;
               try
               {
                   UTF8Encoding utf8 = new UTF8Encoding(false,true);

                   while (true)
                   {
                       int needRead = readLength;
                       if (fileLength - totalBytes &lt; readLength)
                       {
                           needRead = (int)(fileLength - totalBytes);
                       }
                       if (needRead &lt;= 0)
                           break;

                       byte[] fileBuffer = client.read(th, totalBytes, readLength);


                       byte[] myfileBuffer =  Encoding.Convert(utf8, Encoding.GetEncoding(&#34;iso-8859-1&#34;), fileBuffer);
                       
                       
                       totalBytes += readLength;

                       fs.Write(myfileBuffer, 0, myfileBuffer.Length);
                       
                   }
                   result = true;
                   client.close(th);
                  
               }
               catch (Exception ee)
               {
                   throw ee;
               }
               finally
               {
                   fs.Dispose();
                   tsport.Close();
               }
           }
           return result;
       }</pre>

     可以看的，我在将数据流发送至服务器端的时候最终还是用的UTF8编码，接受的时候依然是先UTF8后iso-8859-1存成的文件，您还记得吗？我修改的服务器端代码是用iso-8859-1直接接收和发送的！为什么呢？socket在传输数据的时候自动加了编码？还是Linux在接受数据是加了编码？请知道的大牛给我留言，在此谢过了。 







































 
- - -
 