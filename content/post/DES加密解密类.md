---
title: "DES加密/解密类"
date: 2012-03-09
draft: false
tags: [.net 加密,.net 解密]
categories: [.Net]
---

<pre>		/// &lt;summary&gt; 
		/// 加密数据 
		/// &lt;/summary&gt; 
		/// &lt;param name=&#34;Text&#34;&gt;&lt;/param&gt; 
		/// &lt;param name=&#34;sKey&#34;&gt;&lt;/param&gt; 
		/// &lt;returns&gt;&lt;/returns&gt; 
		public static string Encrypt(string Text,string sKey) 
		{ 
			DESCryptoServiceProvider des = new DESCryptoServiceProvider(); 
			byte[] inputByteArray; 
			inputByteArray=Encoding.Default.GetBytes(Text); 
			des.Key = ASCIIEncoding.ASCII.GetBytes(System.Web.Security.FormsAuthentication.HashPasswordForStoringInConfigFile(sKey, &#34;md5&#34;).Substring(0, 8)); 
			des.IV = ASCIIEncoding.ASCII.GetBytes(System.Web.Security.FormsAuthentication.HashPasswordForStoringInConfigFile(sKey, &#34;md5&#34;).Substring(0, 8)); 
			System.IO.MemoryStream ms=new System.IO.MemoryStream(); 
			CryptoStream cs=new CryptoStream(ms,des.CreateEncryptor(),CryptoStreamMode.Write); 
			cs.Write(inputByteArray,0,inputByteArray.Length); 
			cs.FlushFinalBlock(); 
			StringBuilder ret=new StringBuilder(); 
			foreach( byte b in ms.ToArray()) 
			{ 
				ret.AppendFormat(&#34;{0:X2}&#34;,b); 
			} 
			return ret.ToString(); 
		} 


/// &lt;summary&gt; 
		/// 解密数据 
		/// &lt;/summary&gt; 
		/// &lt;param name=&#34;Text&#34;&gt;&lt;/param&gt; 
		/// &lt;param name=&#34;sKey&#34;&gt;&lt;/param&gt; 
		/// &lt;returns&gt;&lt;/returns&gt; 
		public static string Decrypt(string Text,string sKey) 
		{ 
			DESCryptoServiceProvider des = new DESCryptoServiceProvider(); 
			int len; 
			len=Text.Length/2; 
			byte[] inputByteArray = new byte[len]; 
			int x,i; 
			for(x=0;x&lt;len;x++) 
			{ 
				i = Convert.ToInt32(Text.Substring(x * 2, 2), 16); 
				inputByteArray[x]=(byte)i; 
			} 
			des.Key = ASCIIEncoding.ASCII.GetBytes(System.Web.Security.FormsAuthentication.HashPasswordForStoringInConfigFile(sKey, &#34;md5&#34;).Substring(0, 8)); 
			des.IV = ASCIIEncoding.ASCII.GetBytes(System.Web.Security.FormsAuthentication.HashPasswordForStoringInConfigFile(sKey, &#34;md5&#34;).Substring(0, 8)); 
			System.IO.MemoryStream ms=new System.IO.MemoryStream(); 
			CryptoStream cs=new CryptoStream(ms,des.CreateDecryptor(),CryptoStreamMode.Write); 
			cs.Write(inputByteArray,0,inputByteArray.Length); 
			cs.FlushFinalBlock(); 
			return Encoding.Default.GetString(ms.ToArray()); 
		} </pre>

 
- - -
 