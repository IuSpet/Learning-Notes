# Android学习笔记——AlertDialog

### 默认样式

```java
AlertDialog.Builder builder = new AlertDialog.Builder(DialogActivity.this);
                    builder.setTitle("answer the question").setMessage("how do you do").setPositiveButton("goodjob", new DialogInterface.OnClickListener() {
     @Override
     public void onClick(DialogInterface dialog, int which) {
     Toast.makeText(getApplicationContext(), "honest", Toast.LENGTH_LONG).show();} }).show();
```

​		链式设置内容，最后要记得调用show方法

### 单选样式

```java
final String[] gender = new String[]{"male", "female"};
AlertDialog.Builder builder1 = new AlertDialog.Builder(DialogActivity.this);
builder1.setTitle("选择性别").setItems(gender, new DialogInterface.OnClickListener() {
    @Override
    public void onClick(DialogInterface dialog, int which) {
    Toast.makeText(DialogActivity.this, gender[which], Toast.LENGTH_LONG).show();}
}).show();
```

​		用 `setItems` 方法传入字符串数组作为单选选项，在监听方法中，which对应数组中的下标

多选样式

自定义

### 常用方法

#### 点击屏幕其他位置对话框不消失

​		在builder对象上调用 `setCancelable(false)` 

#### 点击后选项后消失

​		在点击监听器内调用 `dialog.dismiss()` 