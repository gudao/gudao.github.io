---
title: "c#调用c/c++类库"
date: 2013-12-11
draft: false
tags: [c#,c++,IntPtr,Marshal]
categories: [.Net]
---

接触c#久了难免接触其他语言的类库，懂得了类型的对应关系可能对以后的学习带来点便利。



<table height="116" bordercolor="#000000"><tbody><tr><td>C++</td><td>C#</td></tr><tr><td>int*</td><td>ref int</td></tr><tr><td>int</td><td>int</td></tr><tr><td>unsigned int</td><td>uint</td></tr><tr><td>const char*</td><td>string</td></tr><tr><td>const void*</td><td>IntPtr</td></tr><tr><td>返回值 const char *</td><td>IntPtr</td></tr></tbody></table>



下面举例说明IntPtr的用法：

写入：




<pre> byte[] buff = new byte[1024];
IntPtr bp = Marshal.AllocHGlobal(1024);
...//写入buff
Marshal.Copy(buff, 0, bp, buff.Length);</pre>

读取：





<pre>        /// &lt;summary&gt;
        /// 把结构体转化为字节序列
        /// &lt;/summary&gt;
        /// &lt;param name=&#34;structure&#34;&gt;被转化的结构体&lt;/param&gt;
        /// &lt;returns&gt;返回字节序列&lt;/returns&gt;
        Byte[] StructToBytes(Object structure)
        {
            Int32 size = Marshal.SizeOf(structure);
            IntPtr buffer = Marshal.AllocHGlobal(size);
            try
            {
                Marshal.StructureToPtr(structure, buffer, false);
                Byte[] bytes = new Byte[size];
                Marshal.Copy(buffer, bytes, 0, size);
                return bytes;
            }
            finally
            {
                Marshal.FreeHGlobal(buffer);
            }
        }

        /// &lt;summary&gt;
        /// 指针转字符串
        /// &lt;/summary&gt;
        /// &lt;param name=&#34;p&#34;&gt;指向非托管代码字符串的指针&lt;/param&gt;
        /// &lt;returns&gt;返回指针指向的字符串&lt;/returns&gt;
        public static string Ptr2Str(IntPtr p)
        {
            List&lt;byte&gt; lb = new List&lt;byte&gt;();
            while (Marshal.ReadByte(p) != 0)
            {
                lb.Add(Marshal.ReadByte(p));
                p = p + 1;
            }
            byte[] bs = lb.ToArray();
            return Encoding.Default.GetString(lb.ToArray());
        }</pre>


 
- - -
 