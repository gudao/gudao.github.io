---
title: "WinForm ListView不分页加载大量数据"
date: 2013-06-19
draft: false
tags: [ListView,VirtualMode,大数据量闪烁]
categories: [.Net]
---

![](/Content/upload/Img20130619/s_e32ccvrn_lb1.jpg) 
WinForm的ListView在加载大量数据时会出现闪烁的问题，同时数据加载很慢。如果你的列表中有超过千条的数据且不做特殊处理还是用普通的ListView.Items.Add()，估计你的用户得抱怨了。

下面说下解决方法：

1、使用listView1.Items.AddRange()代替Add

这种方法需要先将数据放入一个缓存数组中，然后调用AddRange一次性加入ListView中，同时可以用一个计数器记录一次性加入缓存的数量，如下：




<pre>             listView1.Items.Clear();
            if (vList.Count &gt; 0)
            {
                
                int indexI = 0;
                List&lt;ListViewItem&gt; listBuffer = new List&lt;ListViewItem&gt;();
                foreach (var item in vList)
                {
                    ListViewItem li = new ListViewItem();
                    li.ImageIndex = 0;
                    li.SubItems[0].Text = item.Name;
                    li.Tag = item;
                    li.ForeColor = item.Status == 0 ? Color.Green : Color.Red;
                    listBuffer.Add(li);
                    
                    if (indexI++ % 1000 == 0)
                    {
                        listView1.Items.AddRange(listBuffer.ToArray());
                        listBuffer.Clear(); 
                    }
                    if (indexI % 50 == 0)
                    {
                        Application.DoEvents();
                    }
                }
                listView1.Items.AddRange(listBuffer.ToArray());

            }</pre>

这样可以减少ListView闪烁的次数，数据量不是很大时有效果。


2、自定义ListView类

下面这个类在网上广为流传，虽然解决了ListView闪烁的问题，但是在打开速度上和原来没什么区别，同时带来一个问题就是如果程序切换到别的ListView上，数据还会继续忘原来的ListView中添加，直到数据全面添加完成。




<pre>    public class ListViewLargeData : System.Windows.Forms.ListView
    {
        public ListViewLargeData()
        {
            this.SetStyle(ControlStyles.OptimizedDoubleBuffer | ControlStyles.AllPaintingInWmPaint, true);
            this.SetStyle(ControlStyles.EnableNotifyMessage, true);
        }
        protected override void OnNotifyMessage(Message m)
        {
            if (m.Msg != 0x14)
            {
                base.OnNotifyMessage(m);
            }
        }
    }</pre>

3、开启ListView的VirtualMode模式


  此方式也是本文重点推荐的方式，可以实现不闪烁效果，而且打开速度很快。往往好的效果带来的问题是代码较为复杂，具体写法可以看官方示例http://msdn.microsoft.com/zh-cn/library/system.windows.forms.listview.virtualmode.aspx，或者http://www.cnblogs.com/hcfalan/archive/2008/07/08/1238493.html，下面说下需要注意的几点：

    (1)必须设置VirtualMode为true并设置VirtualListSize大小

            listView1.VirtualMode = true;            listView1.VirtualListSize = bufferItems.Count;            listView1.RetrieveVirtualItem += new RetrieveVirtualItemEventHandler(listView_RetrieveVirtualItem);

    (2)绑定该事件为ListView计算Item

        void listView_RetrieveVirtualItem(object sender, RetrieveVirtualItemEventArgs e)        {            e.Item = m_hListViewItems[e.ItemIndex];        }

    (3)如果中间更新了数据需要重新设置VirtualListSize，并调用Invalidate()方法（此方法并非必须请高手指点）。

            listView1.VirtualListSize = bufferItems.Count;            listView1.Invalidate();

    (4)禁用selectedItem，在该模式下使用selectedItem将产生异常，可以用下面方法代替

         private List&lt;ListViewItem&gt; FindSelectedAll()        {            List&lt;ListViewItem&gt; r = new List&lt;ListViewItem&gt;();            foreach (int item in listView1.SelectedIndices)            {                r.Add(bufferItems[item]);            }            return r;        }




 
- - -
 