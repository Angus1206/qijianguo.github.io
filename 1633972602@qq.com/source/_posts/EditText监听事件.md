---
title: EditText监听事件
categories: Android
tags: android,EditText
---

* 源码
```[源码]
   /**
     * Adds a TextWatcher to the list of those whose methods are called
     * whenever this TextView's text changes.
     * <p>
     * In 1.0, the {@link TextWatcher#afterTextChanged} method was erroneously
     * not called after {@link #setText} calls.  Now, doing {@link #setText}
     * if there are any text changed listeners forces the buffer type to
     * Editable if it would not otherwise be and does call this method.
     */
 public void addTextChangedListener(TextWatcher watcher) {
      if (mListeners == null) {
           mListeners = new ArrayList<TextWatcher>();
      }

      mListeners.add(watcher);
  }
```

* 示例: 

```
private EditText editText;

private void initView(){
	editText = (editText)findViewById(R.id.edit_text);
}

private void initEvents() {
	editText.addTextChangedListener(new TextWatcher() {
            @Override
            public void beforeTextChanged(CharSequence s, int start, int count, int after) {
                Log.i(TAG, "beforeTextChanged: 输入过程中执行该方法");
            }

            @Override
            public void onTextChanged(CharSequence s, int start, int before, int count) {
                Log.i(TAG, "onTextChanged: 输入前确认执行该方法");
            }

            @Override
            public void afterTextChanged(Editable s) {
		Log.i(TAG, "afterTextChanged: 输入结束执行该方法");
            }
        });

}
```
