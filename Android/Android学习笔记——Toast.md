# Android学习笔记——Toast

- Toast是一个消息提示组件

- Toast设置显示的位置

- 自定义显示内容

- 简单封装

### 默认使用

```java
//上下文对象，字符串，间隔
Toast.makeText(Context context, CharSequence text, @Duration int duration).show();
//例子，弹出“Toast1”提示，停留2s后消失
Toast.makeText(getApplicationContext(),"Toast1",Toast.LENGTH_LONG).show();
```

### 改变位置

使用Toast的setGravity()方法

```java
//在屏幕中央显示toast
Toast toastCenter = Toast.makeText(getApplicationContext(),"toast",Toast.LENGTH_LONG );
toastCenter.setGravity(Gravity.CENTER,0,0);
toastCenter.show();
```

### 自定义显示内容

```java
Toast toastCustom = new Toast(getApplicationContext());
LayoutInflater inflater = LayoutInflater.from(ToastActivity.this);
//自定义的toast布局文件
View view = inflater.inflate(R.layout.layout_toast, null);
//绑定控件
ImageView imageView = view.findViewById(R.id.iv_toast);
TextView textView = view.findViewById(R.id.tv_toast);
//设置内容
imageView.setImageResource(R.drawable.toast);
textView.setText("custom toast");
//将view设置成toast的样式
toastCustom.setView(view);
//show方法展示toast
toastCustom.show();
```

