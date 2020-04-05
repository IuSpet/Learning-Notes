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

### 多选样式

```java
final String[] array = new String[]{"1","2","3"};
boolean[] isSelected = new boolean[]{true, false, false};
AlertDialog.Builder builder4 = new AlertDialog.Builder(DialogActivity.this);
 builder4.setTitle("title").setMultiChoiceItems(hobbies4, isSelected, new DialogInterface.OnMultiChoiceClickListener() {
                        @Override
                        public void onClick(DialogInterface dialog, int which, boolean isChecked) {
                            Toast.makeText(DialogActivity.this, hobbies4[which] + ": " + isChecked, Toast.LENGTH_LONG).show();
                        }
                    }).show();
```

- 定义选项列表
- 定义选项默认选取情况
- `AlertDialog.Builder` 实例调用 `setMultiChoiceItems` 方法设置多选Dialog

#### 自定义

首先自定义一个布局文件

```java
AlertDialog.Builder builder5 = new AlertDialog.Builder(DialogActivity.this);
View view = LayoutInflater.from(DialogActivity.this).inflate(R.layout.layout_dialog, null);
EditText etUserName = view.findViewById(R.id.et_username);
EditText etPassWord = view.findViewById(R.id.et_password);
Button btnLogin = view.findViewById(R.id.btn_login);
btnLogin.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View v) {
    Toast.makeText(DialogActivity.this, "login", Toast.LENGTH_SHORT).show();
}
});
builder5.setTitle("log in").setView(R.layout.layout_dialog).show();
```

​		然后用 `LayoutInflater.from(<当前布局activity>).inflate(<布局文件id>,null)` 获取布局文件的view对象

​		然后声明view内的控件，设置监听器等内容，最后将view作为参数传入 `AlertDialog.Builder` 对象的 `setView` 方法内

### 常用方法

#### 点击屏幕其他位置对话框不消失

​		在builder对象上调用 `setCancelable(false)` 

#### 点击后选项后消失

​		在点击监听器内调用 `dialog.dismiss()` 