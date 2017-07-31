---
title: Recycler下Adapter的HeaderFooter多个添加方式 ##文章标题
date: 2017-7-12  00:19:37
## updated: 2017-7-12 13:19:37
comments: true ##开启评论 false关闭
tags:
    - adapter
categories:
    - android
---

# Header Footer添加的问题

因为要撸库 所有参考了很多adapter库的代码 但是都发现一个问题 star前5的的Recycler Adapter库的Header Footer要么只能添加一次(在这里面添加删除view),要么添加之后删除会错位

> 为什么会错位呢?因为他们用List<ViewDelegates> mHeaderViews的position 映射后当getType的值。
> 当删除一个headView的时候postion则会错位。如果做这样的以一个操作 删除头部然后添加另一个头部。布局不同。那么会发现视图根本不会改变还是原来的头部布局。因为getType的没变的缘故。
> 当然可以通过其他的方式 让position不错位。具体可看 [ZAdapter3](https://github.com/luhaoaimama1/ZAdapter3)的1.0分支。但是还是有问题 滚动动画会出现奇怪的问题。所以弃用!

怎么改变这一现状能让添加头部和尾部如同listView一样呢。参考了ListView发现所有类型都是一个;而后我有想到 viewHold的机制其实就是复用。那我给头部和尾部都添加一个空的FrameLayout。然后在bindView是头尾的时候 removeAllViews,和addView(header/footer) 不就可以了吗?


# 这里我给出关键性代码:

[完整代码](https://github.com/luhaoaimama1/ZAdapter3/blob/master/zadapter3/src/main/java/com/zone/adapter3/base/Header2FooterRcvAdapter.java)

```
    public static final int ITEM_VIEW_TYPE_HEADER_OR_FOOTER = -3;

    //Limit one thousand
    private List<ViewDelegates> mHeaderViews = new ArrayList<>();
    private List<ViewDelegates> mFooterViews = new ArrayList<>();


   @Override
    public Holder onCreateViewHolder(final ViewGroup parent, int viewType) {
        if (viewType == ITEM_VIEW_TYPE_HEADER_OR_FOOTER) {
            QuickConfig.e("onCreateViewHolder header or footer :" + viewType);
            return new Holder(setFullspan(LayoutInflater.from(context)
                    .inflate(R.layout.base_vp, mRecyclerView, false)));
        }
        ...
    }
    /**
     * @return layoutid
     * 因为getItemViewType不同 导致 头部底部view不会被重用!
     */
    @Override
    public int getItemViewType(int position) {
        ...
        if (position >= getHeaderViewsCount() && position < getHeaderViewsCount() + data.size()) {
            QuickConfig.e("getItemViewType views:" + position);
            int result = getItemViewType2(getDataPosition(position));
            if (result == ITEM_VIEW_TYPE_HEADER_OR_FOOTER)
                throw new IllegalStateException("layoutType is must not be" + ITEM_VIEW_TYPE_HEADER_OR_FOOTER);
            return result;
        } else {
            QuickConfig.e("getItemViewType healder or footer:" + position);
            return ITEM_VIEW_TYPE_HEADER_OR_FOOTER;
        }
    }

    @Override
    public void onBindViewHolder(Holder holder, int position) {
        if (isEmptyData())
            return;
        if (position >= mHeaderViews.size() && position < mHeaderViews.size() + data.size()) {
            if (holder.wrapper != null)
                holder.wrapper.getViewDelegates().fillData(
                        position, data.get(getDataPosition(position)),
                        holder.helper);
        } else if (position < mHeaderViews.size()) {
            QuickConfig.e("bind header position:" + position);
            bindHFView((ViewGroup) holder.itemView, mHeaderViews.get(position).getItemView());
        } else {
            QuickConfig.e("bind footer position:" + position);
            bindHFView((ViewGroup) holder.itemView, mFooterViews.get(position - getHeaderViewsCount() - data.size()).getItemView());
        }
    }

    private void bindHFView(ViewGroup parent, View child) {
        parent.removeAllViews();
        ViewGroup vp = (ViewGroup) child.getParent();
        if (vp != null)
            vp.removeAllViews();
        parent.addView(child);
    }

```


