---
title: Android-事件传递的学习 ##文章标题
date: 2016-9-17 13:19:37
## updated: 2016-10-17 13:19:37
comments: true ##开启评论 false关闭
tags:
    - android
    - 事件传递

categories:
    - android
---
研究分发流程,总结为伪代码便于理解,然后研究源码,最后探究Method之requestDisallowInterceptTouchEvent

<!-- more -->
# dispatchTouchEvent

## View总结:

>onTouchListener > onTouchEvent > onLongClickListener > onClickListener

>上述伪代码总结为下； 基本关键点已经还原~

```
   public boolean dispatchTouchEvent(MotionEvent ev) {
        boolean result = false;
        if (onTouchListener != null)
            result = onTouch();
        if (!result) {
            result = onTouchEvent(e) {
                // 检查各种 clickable 如果有一个会 是true就会走
                if (((viewFlags & CLICKABLE) == CLICKABLE ||
                        (viewFlags & LONG_CLICKABLE) == LONG_CLICKABLE) ||
                        (viewFlags & CONTEXT_CLICKABLE) == CONTEXT_CLICKABLE) {
                    
                    case:down
                    mHasPerformedLongPress = false;//设置标识
                    postDelay（mPendingCheckForTap, ViewConfiguration.getLongPressTimeout()）
                    //mPendingCheckForTap线程执行后更改为true; 并执行onLongClickListener
                    
                    case:up
                    if (!mHasPerformedLongPress) {
                        removeLongPressCallback();  // 移除长按
                        执行onClickListener
                    }
                }
                return true;                        // ◀︎表示事件被消费
            }
            return false;
        }
        return result;
   }
```

## ViewGroup总结：

```
 public boolean dispatchTouchEvent(MotionEvent ev) {
      boolean handled=false,intercepted= false;
       //--------拦截判断--------
      //disallowIntercept与requestDisallowInterceptTouchEvent(boolean)设置的值有关；
      if(disallowIntercept)
        intercepted = onInterceptTouchEvent(ev)
      else
        intercepted=false;
      //--------拦截结束--------
      if (!intercepted) {
      //不拦截的话
         for (int i = childrenCount - 1; i >= 0; i--) {
                  //如果有上一个dispatch返回true了 这个直接跳出for循环；
                  if (newTouchTarget != null) 
                         break;
                  //dispatchTransformedTouchEvent:这个就是会调用child.dispatchTouchEvent
                  if (dispatchTransformedTouchEvent(ev, false, child, idBitsToAssign)) {
                    //addTouchTarget方法里会给mFirstTouchTarget赋值；
                    newTouchTarget = addTouchTarget(child, idBitsToAssign);
                    break;
                  }
             }
      }
    if (mFirstTouchTarget == null) { 
        //dispatchTransformedTouchEvent 因为传递null的view最后会走 super(就是view).dispatchTouchEvent()方法
        handled = dispatchTransformedTouchEvent(ev, canceled, null,
                TouchTarget.ALL_POINTER_IDS);
    } 
        
 return handled;
 }
```


# 源码探究
View dispatchTouchEvent
```
 public boolean dispatchTouchEvent(MotionEvent event) {
     boolean result = false;
     if (onFilterTouchEventForSecurity(event)) {
               //noinspection SimplifiableIfStatement
               ListenerInfo li = mListenerInfo;
               if (li != null && li.mOnTouchListener != null
                       && (mViewFlags & ENABLED_MASK) == ENABLED
                       && li.mOnTouchListener.onTouch(this, event)) {
                    //如果 不是ENABLED 并且 setOnTouchListener
                   result = true;
               }
   
               if (!result && onTouchEvent(event)) {
               // 如果设置了setOnTouchListener 会截断(就跳过去了)onTouchEvent 
                   result = true;
               }
       }
     return result; 
   }
```

## View onTouchEvent 伪代码 
```
public boolean onTouchEvent(MotionEvent event) {
    ...
    final int action = event.getAction();
  	// 检查各种 clickable 如果有一个会 是true就会走
    if (((viewFlags & CLICKABLE) == CLICKABLE ||
            (viewFlags & LONG_CLICKABLE) == LONG_CLICKABLE) ||
            (viewFlags & CONTEXT_CLICKABLE) == CONTEXT_CLICKABLE) {
        switch (action) {
            case MotionEvent.ACTION_UP:
                ...
                if(!mHasPerformedLongPress){
                //longClick线程执行的时候会修改mHasPerformedLongPress=true;
                //所以longClick 和 click不会同时触发；
                    removeLongPressCallback();  // 移除长按
                    ...
                    performClick();             // 检查单击
                }
                ...
                break;
            case MotionEvent.ACTION_DOWN:
                mHasPerformedLongPress=false;
                ...
                checkForLongClick(0);       // 检测长按 这是个延时线程如果没有被移除 就会执行；
                ...
                break;
            ...
        }
        return true;                        // ◀︎表示事件被消费
    }
    return false;
}
```
>摘自GcsSloop:http://www.gcssloop.com/customview/dispatch-touchevent-source


## ViewGroup 
>extend View

```
 @Override
    public boolean dispatchTouchEvent(MotionEvent ev) {
    ...
    boolean handled = false;
    ... 
    //--------拦截判断--------
    final boolean intercepted;
          if (actionMasked == MotionEvent.ACTION_DOWN
                  || mFirstTouchTarget != null) {
               //disallowIntercept与requestDisallowInterceptTouchEvent(boolean)方法有关；
              final boolean disallowIntercept = (mGroupFlags & FLAG_DISALLOW_INTERCEPT) != 0;
              if (!disallowIntercept) {
                  intercepted = onInterceptTouchEvent(ev);
                  ev.setAction(action); // restore action in case it was changed
              } else {
                  intercepted = false;
              }
          } else {
              // There are no touch targets and this action is not an initial down
              // so this view group continues to intercept touches.
              intercepted = true;
          }  
      //--------拦截结束--------
      
      if (!canceled && !intercepted) {
        //没被拦截的情况
        ...
        for (int i = childrenCount - 1; i >= 0; i--) {
            ...
             //如果有上一个dispatch返回true了 这个直接跳出for循环；
             if (newTouchTarget != null) {
                    // Child is already receiving touch within its bounds.
                    // Give it the new pointer in addition to the ones it is handling.
                    newTouchTarget.pointerIdBits |= idBitsToAssign;
                    break;
             }
             if (dispatchTransformedTouchEvent(ev, false, child, idBitsToAssign)) {
               // Child wants to receive touch within its bounds.
               mLastTouchDownTime = ev.getDownTime();
               if (preorderedList != null) {
                   // childIndex points into presorted list, find original index
                   for (int j = 0; j < childrenCount; j++) {
                       if (children[childIndex] == mChildren[j]) {
                           mLastTouchDownIndex = j;
                           break;
                       }
                   }
               } else {
                   mLastTouchDownIndex = childIndex;
               }
               mLastTouchDownX = ev.getX();
               mLastTouchDownY = ev.getY();
               //addTouchTarget方法里会给mFirstTouchTarget赋值；
               newTouchTarget = addTouchTarget(child, idBitsToAssign);
               alreadyDispatchedToNewTouchTarget = true;
               break;
             }
        }
         ...
      }
      
      // Dispatch to touch targets.
      if (mFirstTouchTarget == null) { 
          // No touch targets so treat this as an ordinary view.
          //dispatchTransformedTouchEvent 因为传递null的view最后会走 super(就是view).dispatchTouchEvent()方法
          handled = dispatchTransformedTouchEvent(ev, canceled, null,
                  TouchTarget.ALL_POINTER_IDS);
      } 
     ...
     return handled; 
    }
```



# Method之requestDisallowInterceptTouchEvent
>通过日志进行研究

* 已知:
    * ReParent(就是ViewGroup): onTouchEvent,dispatchTouchEvent,onInterceptTouchEvent 
    * ViewChild(就是View): onTouchEvent,dispatchTouchEvent

>return 的都是super.

## 开始测试：
* 第一次 修改child  onTouchEvent return true 

    ```
    ReParent->dispatchTouchEvent ACTION_MOVE
    ReParent->onInterceptTouchEvent ACTION_MOVE
    ViewChild->dispatchTouchEvent ACTION_MOVE
    ViewChild->onTouchEvent ACTION_MOVE
    ```
    >说明 即使我消耗了,其父dispatch,onInterceptTouchEvent也会相继走,
    
    >而不是直接跳到目标View执行 dispatchTouchEvent,onTouchEvent 与传言不符~

* 第二次 child dispatchTouchEvent return true 

    ```
    ReParent->dispatchTouchEvent ACTION_MOVE
    ReParent->onInterceptTouchEvent ACTION_MOVE
    ViewChild->dispatchTouchEvent ACTION_MOVE
    ```
    >为啥没走  ReParent->onTouchEvent ACTION_DOWN 因为 addTouchTarget(child, idBitsToAssign)是dispatchTouchEvent return true就执行 

    >会找到对应的目标;所以下面代码不走 
    
    ```
    源码
    if (mFirstTouchTarget == null) 
            // No touch targets so treat this as an ordinary view.
            handled = dispatchTransformedTouchEvent(ev, canceled, null,
                    TouchTarget.ALL_POINTER_IDS);
    ```

* 第三次：  child dispatchTouchEvent return true  但是我没有触碰到child 
 
    ```
        ReParent->dispatchTouchEvent ACTION_DOWN
        ReParent->onInterceptTouchEvent ACTION_DOWN
        ReParent->onTouchEvent ACTION_DOWN
    ```
    >说明 第二次的源码是正确执行的;

* `requestDisallowInterceptTouchEvent探究1`
    >把代码改回 child onTouchEvent return true dispatchTouchEvent return super.dispatchTouchEvent

    测试源码
    ```
                case MotionEvent.ACTION_DOWN :
                    downX =event.getX();
                    Log.i(TAG, "ViewChild->onTouchEvent ACTION_DOWN");
                    break;
                case MotionEvent.ACTION_MOVE:
                    if(Math.abs(event.getX()-downX)>200){
                        Log.i(TAG, "ViewChild- >200!!! 请求拦截");
                        getParent().requestDisallowInterceptTouchEvent(false);
                    }
                    else{
                        Log.i(TAG, "ViewChild- <200!!! 父亲不拦截");
                        getParent().requestDisallowInterceptTouchEvent(true);
                    }
                    Log.i(TAG, "ViewChild->onTouchEvent ACTION_MOVE");
                    break;
    ```
    log:
    ```
    ViewChild- <200!!! 父亲不拦截
    ViewChild->onTouchEvent ACTION_MOVE
    ReParent->dispatchTouchEvent ACTION_MOVE
    ViewChild->dispatchTouchEvent ACTION_MOVE
    
    ViewChild- >200!!! 请求拦截
    ViewChild->onTouchEvent ACTION_MOVE
    ReParent->dispatchTouchEvent ACTION_MOVE
    ReParent->onInterceptTouchEvent ACTION_MOVE
    ViewChild->dispatchTouchEvent ACTION_MOVE
    
    ViewChild- <200!!! 父亲不拦截
    ViewChild->onTouchEvent ACTION_MOVE
    ReParent->dispatchTouchEvent ACTION_MOVE
    ViewChild->dispatchTouchEvent ACTION_MOVE
    ```


* `requestDisallowInterceptTouchEvent 探究2`
    >把ReParent onInterceptTouchEvent return true
     
    ```
        ReParent->dispatchTouchEvent ACTION_DOWN
        ReParent->onInterceptTouchEvent ACTION_DOWN
        ReParent->onTouchEvent ACTION_DOWN
    ```
    >说明 requestDisallowInterceptTouchEvent代码如果不走是不能让他停止拦截的；

