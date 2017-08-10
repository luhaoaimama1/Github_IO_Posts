---
title: ZAdapter3断头吸附效果 ##文章标题
date: 2017-8-11 00:19:37
## updated: 2017-3-14 13:19:37
comments: true ##开启评论 false关闭
tags:
    - adapter
categories:
    - android
---

# 几种方案是选择

 * 外层一个显示的断头和内部的布局一样,但不是同一个  因为涉及到选择状态和 横向滚动 而导致的复杂性 放弃！

 * 自定义recyclerView布局,这样解决了是同一个的view的问题。但是因为getTop而导致 刷新监听的不兼容 放弃！

 * 既然想要用同一个View的话可以用李代桃僵的方式去实现，既用一个空view去代替RecyclerView的Itemview站位。然后吧itemView显示在断头上即可！


# 效果图

![](https://ww1.sinaimg.cn/large/006tNc79ly1fifdm9jzucg307i0dcal7.gif)


<!-- more -->


# demo
```
public class AdsorbActivity extends Activity {

    private List<String> mDatas = new ArrayList<String>();
    private RecyclerView rv;
    private IAdapter<String> muliAdapter;
    private FrameLayout vp;


    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.a_recycler_absorb);
        rv = (RecyclerView) findViewById(R.id.rv);
        vp = (FrameLayout) findViewById(R.id.vp);
        rv.setLayoutManager(new LinearLayoutManager(this));
        for (int i = 1; i <= 100; i++) {
            mDatas.add("" + i);
        }
        rv.addOnScrollListener(new AbsorbOnScrollListener(vp, 3, 6, 9));

        muliAdapter = new QuickRcvAdapter(this, mDatas) {
            @Override
            protected int getItemViewType2(int dataPosition) {
                if (dataPosition == 3)
                    return 1;
                else if (dataPosition == 6)
                    return 2;
                else if (dataPosition == 9)
                    return 3;
                else
                    return 0;
            }
        };
        muliAdapter
                .addViewHolder(new LeftDelegates())//默认
                .addViewHolder(0, new LeftDelegates()) //多部剧 注释开启即可
                .addViewHolder(1, new AbsorbDelegates())//多部剧 注释开启即可
                .addViewHolder(2, new AbsorbDelegates2())//多部剧 注释开启即可
                .addViewHolder(3, new AbsorbDelegates())//多部剧 注释开启即可
//                .addHeaderHolder(R.layout.header_simple)
//                .addFooterHolder(R.layout.footer_simple)
                .addEmptyHold(R.layout.empty)
                .relatedList(rv)
                .addItemDecoration(70)
                .setOnItemClickListener(new IAdapter.OnItemClickListener() {
                    @Override
                    public void onItemClick(ViewGroup parent, View view, int position) {
                        System.out.println("被点击->onItemClick" + position);
                    }
                })
                .setOnItemLongClickListener(new IAdapter.OnItemLongClickListener() {
                    @Override
                    public boolean onItemLongClick(ViewGroup parent, View view, int position) {
                        System.out.println("被点击->onItemLongClick:" + position);
                        return true;
                    }
                });
    }
}
```

# 代码实现

```

public class AbsorbOnScrollListener extends RecyclerView.OnScrollListener {

    private final FrameLayout vpShow;
    private int[] absorbPos;
    private View[] absorbViews;
    private FrameLayout[] itemViews;
    private View placeholderView;
    private View contentView;

    public AbsorbOnScrollListener(FrameLayout vpShow, int... absorbPos) {
        if (absorbPos == null || absorbPos.length == 0)
            throw new IllegalStateException("absorbPos is not null or empty");
        this.absorbPos = absorbPos;
        this.vpShow = vpShow;
        absorbViews = new View[absorbPos.length];
        itemViews = new FrameLayout[absorbPos.length];
    }

    @Override
    public void onScrollStateChanged(RecyclerView recyclerView, int newState) {
        super.onScrollStateChanged(recyclerView, newState);
    }

    @Override
    public void onScrolled(RecyclerView recyclerView, int dx, int dy) {
        super.onScrolled(recyclerView, dx, dy);

        int pos = 0;
        try {
            //GridLayoutManager 继承LinearLayoutManager 所以也支持GridLayoutManager
            pos = ((LinearLayoutManager) (recyclerView.getLayoutManager())).findFirstVisibleItemPosition();
        } catch (Exception e) {
            e.printStackTrace();
            throw new IllegalStateException("仅仅支持继承LinearLayoutManager的布局!");
        }

        //缓存list中断头view
        for (int i = 0; i < absorbPos.length; i++) {
            if (pos == absorbPos[i] && itemViews[i] == null) {
                try {
                    itemViews[i] = (FrameLayout) recyclerView.findViewHolderForLayoutPosition(pos).itemView;
                } catch (Exception e) {
                    e.printStackTrace();
                    throw new IllegalStateException("断头的itemView必须是FrameLayout!");
                }
                absorbViews[i] = itemViews[i].getChildAt(0);
            }
        }
        int showPos = findShowPos(pos);

        if (showPos == -1) {//啥也不显示
            if (placeholderView != null && placeholderView.getParent() != null) {
                restoreItemView(0);
                contentView = null;
            }
        } else {
            if (placeholderView == null)
                placeholderView = new View(recyclerView.getContext());

            //找到对应的位置
            int arrayPos = 0;
            for (int i = 0; i < absorbPos.length; i++) {
                if (showPos == absorbPos[i]) {
                    arrayPos = i;
                    break;
                }
            }

            if (!(contentView != null && contentView == absorbViews[arrayPos])) {
                //如果断头view不改变的话仅仅更改位置  主要是节省了操作

                //找到之前那个itemView 还原了
                for (int i = 0; i < absorbViews.length; i++) {
                    if (absorbViews[i] != null && absorbViews[i] == contentView) {
                        restoreItemView(i);
                        break;
                    }
                }

                //ItemView的狸猫换太子 显示在VP中
                itemViews[arrayPos].removeView(absorbViews[arrayPos]);
                placeholderView.setLayoutParams(absorbViews[arrayPos].getLayoutParams());
                placeholderView.getLayoutParams().height = absorbViews[arrayPos].getHeight();
                itemViews[arrayPos].addView(placeholderView);
                addContentView(absorbViews[arrayPos]);
            }

            //位移计算!
            if (showPos != absorbPos[absorbPos.length - 1]) {//最后的那个不进行位移
                int next = absorbPos[arrayPos + 1];
                if (recyclerView.findViewHolderForLayoutPosition(next) == null)
                    absorbViews[arrayPos].setTranslationY(0);
                else {
                    View targetView = recyclerView.findViewHolderForLayoutPosition(next).itemView;
                    if (targetView.getTop() <= absorbViews[arrayPos].getHeight())
                        absorbViews[arrayPos].setTranslationY(targetView.getTop() - absorbViews[arrayPos].getHeight());
                }
            }


        }
    }

    private void restoreItemView(int i) {
        itemViews[i].removeView(placeholderView);
        vpShow.removeView(absorbViews[i]);
        absorbViews[i].setTranslationY(0);
        itemViews[i].addView(absorbViews[i]);
    }

    private void addContentView(View absorbView) {
        vpShow.addView(absorbView);
        contentView = absorbView;
    }

    // 3,6,9为例  [1,3)show 无 ,[3,6)show 3,[6,9)show 6,>=9 show 9,
    private int findShowPos(int pos) {
        for (int i = 0; i < absorbPos.length; i++) {
            if (pos < absorbPos[i]) {
                if (i == 0)//[1,3)show 无
                    return -1;
                //[3,6)show 3,[6,9)show 6
                System.out.println("Absorb_ i:" + i + "\t itemViews[i-1].getTop()" + itemViews[i - 1].getTop());
                return findWithOutDecTop(i, "Absorb_显示:" + (i - 1), "Absorb_显示:无", "Absorb_显示:" + (i - 2));
            }
        }
        // >=9 show 9
        return findWithOutDecTop(absorbPos.length, "Absorb_显示  Last:" + (absorbPos.length - 1), "Absorb_显示  Last:无", "Absorb_显示  Last:" + (absorbPos.length - 2));
    }

    private int findWithOutDecTop(int i, String x, String x2, String x3) {
        // getTop如果<=0的时候 才显示这个位置的断头
        if (itemViews[i - 1].getTop() <= 0) {//注意:getTop不包括 dector
            QuickConfig.i(x);
            return absorbPos[i - 1];
        } else {//不然还是显示上一个位置的断头
            if (i - 2 < 0) {//上一个位置是无的情况
                QuickConfig.i(x2);
                return -1;
            }
            QuickConfig.i(x3);
            return absorbPos[i - 2];
        }
    }

}
```


# ZAdapter3 github地址 欢迎star~

https://github.com/luhaoaimama1/ZAdapter3

# 顺便推荐一首歌曲~

https://www.bilibili.com/video/av7043321/
