---
title: 自定义EditText设置图片文字居中
categories: Android
tags: android
date: 2017-10-21 20:44:14
---

* JAVA

> 自定义类继承EditText or AppCompatEditText

<!-- more -->

```
import android.content.Context;
import android.graphics.Paint;
import android.graphics.Rect;
import android.graphics.drawable.Drawable;
import android.support.v7.widget.AppCompatEditText;
import android.util.AttributeSet;
import android.view.View;
import android.widget.TextView;

public class SearchText extends AppCompatEditText {

    private Drawable searchDrawable;
    private int offset;
    private int searchWidth;
    private String hintString;
    private int w;
    private int flag = 0;

    public SearchText(Context context) {
        super(context);
        init();
    }

    public SearchText(Context context, AttributeSet attrs) {
        super(context, attrs);
        init();
    }

    public SearchText(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
        init();
    }

    private void init() {
        setOnFocusChangeListener(new OnFocusChangeListener() {
            @Override
            public void onFocusChange(View v, boolean hasFocus) {
                if (hasFocus) {
                    // 获得焦点
                    setTextAlignment(TextView.TEXT_ALIGNMENT_VIEW_START);
                } else {
                    // 失去焦点
                    setTextAlignment(TextView.TEXT_ALIGNMENT_CENTER);
                }
            }
        });
        setTextAlignment(TextView.TEXT_ALIGNMENT_CENTER);
    }

    @Override
    protected void onLayout(boolean changed, int left, int top, int right, int bottom) {
        super.onLayout(changed, left, top, right, bottom);
        //圈1
        searchWidth = getMeasuredWidth();
        hintString = getHint().toString();
        //圈2
        Paint paint = new Paint();
        Rect rect = new Rect();
        paint.getTextBounds(hintString, 0, hintString.length(), rect);
        w = dip2px(getContext(), rect.width());
        offset = searchWidth / 2 - w * 2;
        if (flag == 0) {
            //圈3  //圈4
            setTextDrawable();
        }
        flag++;
    }

    public static int dip2px(Context context, float dpValue) {
        final float scale = context.getResources().getDisplayMetrics().density;
        return (int) (dpValue * scale + 0.5f);
    }

    @Override
    protected void onTextChanged(CharSequence text, int start, int lengthBefore, int lengthAfter) {
        super.onTextChanged(text, start, lengthBefore, lengthAfter);
        if (searchDrawable == null) {
            getDrawable();
        }
        if (length() > 0) {
            setTextAlignment(TextView.TEXT_ALIGNMENT_VIEW_START);
            setCompoundDrawables(null, null, null, null);
        } else if (length() == 0) {
            setTextAlignment(TextView.TEXT_ALIGNMENT_CENTER);
            setTextDrawable();
        }
    }

    void getDrawable() {
        //圈5
        Drawable[] compoundDrawables = getCompoundDrawables();
        searchDrawable = compoundDrawables[0];
    }

    void setTextDrawable() {
        searchDrawable.setBounds(offset, 0, offset + searchDrawable.getIntrinsicWidth(), searchDrawable.getIntrinsicHeight());
        setCompoundDrawables(searchDrawable, null, null, null);
    }
}
```

* XML(包名改成自己的)

```
 <com.*.*.SearchText
        android:id="@+id/work_detail_deal_research"
        android:layout_width="match_parent"
        android:layout_height="30dp"
        android:background="@color/white"
        android:layout_marginBottom="2dp"
        android:layout_gravity="center"
        android:gravity="center"
        android:drawableLeft="@drawable/research"
        android:hint="搜索"
        android:textSize="@dimen/txtSize_title"
        android:textColor="@color/txt_color1"/>
```

* 图标

> ![search](https://img-blog.csdn.net/20180716152058577?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1OTc0NzU5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70) ![search2x](https://img-blog.csdn.net/20180716152122751?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1OTc0NzU5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)![这里写图片描述](https://img-blog.csdn.net/2018071615215058?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1OTc0NzU5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

* dimon 

```
<dimen name="txtSize_title">16dp</dimen>
```

原文: https://blog.csdn.net/qq_29951983/article/details/78248068
