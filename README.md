# NotificationUtil

* 实现

获取系统通知栏的背景颜色

* 目的

实现自定义的通知栏根据系统系统通知栏的背景颜色实现不同的颜色布局。

* 支持范围

Android 10.x 、Android 9.x、Android 8.x、Android 7.x 、Android 6.x、Android 5.x、Android 4.x

* 为什么Android 7.x及以上版本notification.contentVie的值为NUll,查看源码Notification.Builder:


        public Notification build() {
            ...
            // api-24
            // 
            if (mContext.getApplicationInfo().targetSdkVersion < Build.VERSION_CODES.N
                    && (mStyle == null || !mStyle.displayCustomViewInline())) {
                if (mN.contentView == null) {
                    mN.contentView = createContentView();
                    mN.extras.putInt(EXTRA_REBUILD_CONTENT_VIEW_ACTION_COUNT,
                            mN.contentView.getSequenceNumber());
                }
                if (mN.bigContentView == null) {
                    mN.bigContentView = createBigContentView();
                    if (mN.bigContentView != null) {
                        mN.extras.putInt(EXTRA_REBUILD_BIG_CONTENT_VIEW_ACTION_COUNT,
                                mN.bigContentView.getSequenceNumber());
                    }
                }
                if (mN.headsUpContentView == null) {
                    mN.headsUpContentView = createHeadsUpContentView();
                    if (mN.headsUpContentView != null) {
                        mN.extras.putInt(EXTRA_REBUILD_HEADS_UP_CONTENT_VIEW_ACTION_COUNT,
                                mN.headsUpContentView.getSequenceNumber());
                    }
                }
            }
            ...
            return mN;
        }
    
根据上面的Framework代码可以看出，把targetSdkVersion设置成23后就不会返回Null，但是目前Google Play及中国各大应用市场都有要求targetSdkVersion不低于28，那还是要有办法就行解决，我提供的这个工具就完美解决了这个问题。

*解决Android 7.x及以上版本notification.contentVie的值为NUll的关键代码


    val cv: RemoteViews = (if (notification.contentView == null) {
            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.N) {
                Notification.Builder.recoverBuilder(pContext, notification).createContentView()
            } else {
                null
            }
        } else {
            notification.contentView
        }) ?: return 0



