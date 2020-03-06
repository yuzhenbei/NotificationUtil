package com.dumyapps.demo1.notification

import android.app.Notification
import android.content.Context
import android.graphics.Color
import android.os.Build
import android.util.Log
import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup
import android.widget.FrameLayout
import android.widget.RemoteViews
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity
import androidx.core.app.NotificationCompat
import java.util.*
import kotlin.math.sqrt

/**
 * @Author create by daozi on 2020/3/6 11:06
 * @Email aoaoyi.com@gmail.com
 * version: 1.0
 */
object NotificationUtil {
    private val TAG = NotificationUtil::class.java.simpleName
    private const val sColorThreshold = 180.0
    private const val sDummyTitle = "dummy title"
    private const val sKeyNotificationBarColor = "keyNotificationBarColor"
    private var mTitleColor = 0

    @JvmStatic
    fun isDarkNotificationBar(pContext: Context, init: Boolean): Boolean {
        return !isColorsSimilar(getNotificationColor(pContext))
    }

    private fun isColorsSimilar(pColor: Int, pBaseColor: Int = Color.BLACK): Boolean {
        val simpleBaseColor = pBaseColor or -0x1000000
        val simpleColor = pColor or -0x1000000
        val baseRed = Color.red(simpleBaseColor) - Color.red(simpleColor)
        val baseGreen = Color.green(simpleBaseColor) - Color.green(simpleColor)
        val baseBlue = Color.blue(simpleBaseColor) - Color.blue(simpleColor)
        val shortValue = sqrt(baseRed * baseRed + baseGreen * baseGreen + (baseBlue * baseBlue).toDouble())
        return shortValue < sColorThreshold
    }

    private fun getNotificationColor(pContext: Context): Int {
        return try {
            return (pContext as? AppCompatActivity)?.let { getNotificationColorCompat(it) } ?: getNotificationColorInternal(pContext)
        } catch (e: Exception) {
            Log.d(TAG, "NotificationColor ", e)
            0
        }
    }

    @Suppress("DEPRECATION")
    private fun getNotificationColorCompat(pContext: Context): Int {
        Log.d(TAG, "getNotificationColorCompat ")
        val notification = NotificationCompat.Builder(pContext).build()
        val cv: RemoteViews = (if (notification.contentView == null) {
            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.N) {
                Notification.Builder.recoverBuilder(pContext, notification).createContentView()
            } else {
                null
            }
        } else {
            notification.contentView
        }) ?: return 0
        val layoutId = cv.layoutId
        val notificationRootView = LayoutInflater.from(pContext).inflate(layoutId, null) as? ViewGroup
        val title = notificationRootView?.findViewById<View>(android.R.id.title) as? TextView
        return if (null == title) {
            val textViews = ArrayList<TextView>()
            iteratorView(notificationRootView, object : Filter {
                override fun filter(pView: View?) {
                    if (pView is TextView) {
                        textViews.add(pView)
                    }
                }
            })
            var minTextSize = Int.MIN_VALUE.toFloat()
            var index = 0
            for (i in 0 until textViews.size) {
                val currentTextSize = textViews[i].textSize
                if (currentTextSize > minTextSize) {
                    minTextSize = currentTextSize
                    index = i
                }
            }
            textViews[index].currentTextColor
        } else {
            title.currentTextColor
        }
    }

    @Suppress("DEPRECATION")
    private fun getNotificationColorInternal(pContext: Context): Int {
        Log.d(TAG, "getNotificationColorInternal ")
        val notification = NotificationCompat.Builder(pContext).setContentTitle(sDummyTitle).build()
        val cv: RemoteViews = (if (notification.contentView == null) {
            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.N) {
                Notification.Builder.recoverBuilder(pContext, notification).createContentView()
            } else {
                null
            }
        } else {
            notification.contentView
        }) ?: return 0
        val notificationRootView = cv.apply(pContext, FrameLayout(pContext)) as? ViewGroup
        val title = notificationRootView?.findViewById<View>(android.R.id.title) as? TextView
        return if (null == title) {
            iteratorView(notificationRootView, object : Filter {
                override fun filter(pView: View?) {
                    if (pView is TextView) {
                        if (sDummyTitle == pView.text.toString()) {
                            mTitleColor = pView.currentTextColor
                        }
                    }
                }
            })
            mTitleColor
        } else {
            title.currentTextColor
        }
    }

    private fun iteratorView(pView: View?, pFilter: Filter?) {
        pView?.also { view ->
            pFilter?.also { filter ->
                filter.filter(view)
                if (view is ViewGroup) {
                    for (i in 0 until view.childCount){
                        iteratorView(view.getChildAt(i), pFilter)
                    }
                }
            }
        }
    }

    private interface Filter {
        fun filter(pView: View?)
    }
}
