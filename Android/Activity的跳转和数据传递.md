# Activity的跳转和数据传递

### 显式跳转

#### 方法一

```java
//第一个参数为上下文对象；第二个参数是要跳转的类的Class对象
Intent intent = new Intent(AActivity.this,BActivity.class);
startActivity(intent);
```

#### 方法二

```java
Intent intent = new Intent();
intent.setClass(AActivity.this,BActivity.class);
startActivity(intent);
```

#### 方法三

```java
Intent intent = new Intent();
//另一种获取Class实例的方式
intent.setClassName(AActivity.this,"com.example.helloworld2.jump.BActivity");
startActivity(intent);
```

#### 方法四

```java
Intent intent = new Intent();
intent.setComponent(new ComponentName(AActivity.this,"com.example.helloworld2.jump.BActivity"));
startActivity(intent);
```

### 隐式跳转

#### 方法一

```java
Intent intent = new Intent();
intent.setAction("android.test.BActivity");
startActivity(intent);
```

对应在 `AndroidManifest.xml` 中设置，两边的action要对应，且category不能是启动

```xml
<activity
    android:name=".jump.BActivity"
    android:label="B">
    <intent-filter>
        <action android:name="android.test.BActivity" />
        <category android:name="android.intent.category.DEFAULT" />
    </intent-filter>
</activity>
```

### 传递数据

#### 创建Activity时传递

发送

```java
Intent intent = new Intent(AActivity.this,BActivity.class);
Bundle bundle = new Bundle();
bundle.putString("name","luSpet");
bundle.putInt("age",22);
//intent.putExtra("name","luSpet"),内部自动创建bundle
intent.putExtras(bundle);
startActivity(intent);
```

接收，通过key获得数据

```java
Intent intent = getIntent();
Bundle bundle = intent.getExtras();
String name = bundle.getString("name");
int age = bundle.getInt("age");
```

#### 结束Activity时返回数据

在启动新的Activity时，使用

```java
//第二个参数为requestCode
startActivityForResult(intent,0);
```

启动的Activity结束时，同样利用bundle放入数据

```java
Intent intent1 = new Intent();
intent1.putExtra("title", "I'm back");
//第一个参数为resultCode
setResult(Activity.RESULT_OK, intent1);
```

在之前的Acitvity中，`onActivityResult` 就会得到数据，且可以通过requestCode和resulCode标识数据来源

```java
@Override
    protected void onActivityResult(int requestCode, int resultCode, @Nullable Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        String res = data.getExtras().getString("title");
        Toast.makeText(AActivity.this,res, Toast.LENGTH_SHORT).show();
    }
```

