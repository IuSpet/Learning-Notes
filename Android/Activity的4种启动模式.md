# Activity的4种启动模式

Activity的 `android:launchMode` 属性，

- stardard：标准模式，默认
- singleTop：Task栈顶复用模式

- singleTask：Task栈内复用模式
- singleInstance：全局单例模式

### standard

Activity是任务栈管理的，没启动一个Activity，就会被放入栈中，按返回键，就会从栈顶移除一个Activity

standard是默认的启动模式，及标准模式。每启动一个创建一个

### singleTop

当要启动的目标Activity已经位于栈顶时，不会创建新的实例，会复用栈顶的Activity，并且其onNewIntent() 方法会被调用；如果Activity不再栈顶，就创建新的实例

### singleTask

当要启动的目标Activity已经位于栈内时，不会创建新的实例，会复用栈内的Activity，并调用其onNewIntent() 方法会被调用，**并且该Activity上面的Activity会被清除**；如果栈中没有，就创建新的实例

### singleInstance

全局复用，不管哪个Task栈，只要存在目标Activity，就复用。**每个Activity占有一个新的Task栈**